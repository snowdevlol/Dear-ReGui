# Dear ReGui — Documentação

Biblioteca de UI para Roblox (Luau) inspirada no Dear ImGui.
Esta página cobre o essencial: carregar a biblioteca, criar janelas, usar os elementos, trocar temas e usar o `SetUI`.

> Exemplo completo com todos os elementos: [`Example.lua`](Example.lua)

---

## 1. Carregando a biblioteca

```lua
local REPO = "https://raw.githubusercontent.com/snowdevlol/Dear-ReGui/refs/heads/main/"

local ReGui = loadstring(game:HttpGet(REPO .. "ReGui.lua"))()

-- Os prefabs (modelos visuais) vêm do Prefabs.lua
local BuildPrefabs = loadstring(game:HttpGet(REPO .. "Prefabs.lua"))()
local Root = BuildPrefabs()

ReGui:Init({
    Prefabs = Root
})

print(ReGui:GetVersion())
```

---

## 2. Janelas

```lua
local Window = ReGui:Window({
    Title = "Minha janela",
    Size = UDim2.fromOffset(400, 300),
    Theme = "DarkTheme",   -- opcional (padrão: DarkTheme)
    NoScroll = true,       -- use quando houver um único elemento com Fill
}):Center()
```

### Opções da janela

| Opção | Tipo | Descrição |
|---|---|---|
| `Title` | string | Título da janela |
| `Size` | UDim2 | Tamanho inicial |
| `Theme` | string | Nome do tema (ver seção 5) |
| `NoTitleBar` | boolean | Esconde a barra de título |
| `NoClose` | boolean | Remove o botão de fechar |
| `NoCollapse` | boolean | Remove o botão de recolher |
| `NoResize` | boolean | Impede redimensionar |
| `NoMove` | boolean | Impede arrastar |
| `NoSelect` | boolean | A janela não ganha foco ao clicar |
| `NoScrollBar` | boolean | Esconde a barra de rolagem |
| `NoBackground` | boolean | Remove o fundo da janela |
| `OpenOnDoubleClick` | boolean | Duplo clique na barra recolhe/expande |
| `NoBringToFrontOnFocus` | boolean | Não traz para frente ao focar |
| `MinimumSize` | Vector2 | Tamanho mínimo ao redimensionar |

### Métodos úteis da janela

```lua
Window:Center()                 -- centraliza na tela
Window:SetTheme("BlackTheme")   -- troca o tema da janela
Window:UpdateConfig({ NoMove = true })  -- altera opções depois de criada
Window:ToggleVisibility()       -- mostra/esconde
Window:ToggleCollapsed()        -- recolhe/expande
Window:Remove()                 -- destrói a janela
```

### Janela com abas

```lua
local TabsWindow = ReGui:TabsWindow({
    Title = "Janela com abas",
    Size = UDim2.fromOffset(300, 200),
})

local Tab = TabsWindow:CreateTab({ Name = "Principal" })
Tab:Label({ Text = "Conteúdo da aba" })
```

---

## 3. Elementos

Todos os elementos são criados a partir de uma janela (ou de outro container como `Row`, `TreeNode`, `CollapsingHeader`, `Table`, `Canvas`).

```lua
Window:Label({ Text = "Olá!" })

Window:Button({
    Text = "Clique",
    Callback = function(self) print("clicou") end,
})

Window:Checkbox({
    Label = "Ativar",
    Value = false,
    Callback = function(self, Value) print(Value) end,
})
```

### Lista de elementos

| Categoria | Elementos |
|---|---|
| Texto | `Label`, `BulletText`, `Bullet`, `Error`, `Separator` |
| Botões | `Button`, `SmallButton`, `ArrowButton`, `ImageButton`, `RadioButton` |
| Seleção | `Checkbox`, `Radiobox`, `Combo`, `Dropdown`, `Selectable`, `Keybind` |
| Valores | `SliderInt`, `SliderFloat`, `SliderEnum`, `SliderProgress`, `DragInt`, `DragFloat`, `InputInt` |
| Texto editável | `InputText`, `InputTextMultiline`, `CodeEditor`, `Console` |
| Dados | `ProgressBar`, `PlotHistogram`, `Image`, `Viewport`, `List`, `Table` |
| Layout | `Row`, `Group`, `Indent`, `Canvas`, `ScrollingCanvas`, `Region`, `MultiElement` |
| Containers | `CollapsingHeader`, `TreeNode`, `TabBar`, `TabSelector`, `MenuBar`, `MenuButton` |
| Pop-ups | `PopupCanvas`, `PopupModal`, `Overlay`, `OverlayScroll` |

### Exemplos rápidos

```lua
-- Sliders
Window:SliderInt({ Label = "Inteiro", Value = 5, Minimum = 1, Maximum = 32 })
Window:SliderFloat({ Label = "Float", Minimum = 0, Maximum = 1, Format = "%.2f" })

-- Combo
Window:Combo({
    Label = "Opções",
    Selected = 1,
    Items = { "A", "B", "C" },
    Callback = function(self, Value) print(Value) end,
})

-- Linha com vários botões
local Row = Window:Row()
Row:Button({ Text = "Salvar" })
Row:Button({ Text = "Carregar" })

-- Barra de progresso
local Bar = Window:ProgressBar({ Label = "Carregando...", Value = 80 })
Bar:SetPercentage(50)

-- Keybind com teclas bloqueadas
Window:Keybind({
    Label = "Atalho",
    KeyBlacklist = { Enum.UserInputType.MouseButton1, Enum.KeyCode.Q },
    Callback = function(self, Key) print(Key) end,
})

-- Tooltip
ReGui:SetItemTooltip(MeuElemento, function(Canvas)
    Canvas:Label({ Text = "Dica!" })
end)
```

### Métodos comuns dos elementos

```lua
Checkbox:SetValue(true)   -- define o valor
Checkbox:Toggle()         -- alterna
Checkbox:SetDisabled(true)
Element:Remove()
```

---

## 4. Salvando configurações (Ini)

Dê um `IniFlag` ao elemento para que o valor seja salvo/carregado.

```lua
Window:Checkbox({ IniFlag = "MeuCheckbox", Value = true })

local Salvo = ReGui:DumpIni(true)   -- gera a string de configuração
ReGui:LoadIni(Salvo, true)          -- restaura os valores
```

---

## 5. Temas

Temas disponíveis em `ReGui.ThemeConfigs`:

| Tema | Descrição |
|---|---|
| `DarkTheme` | Tema azul escuro padrão |
| `LightTheme` | Tema claro |
| `ImGui` | Visual clássico do Dear ImGui |
| `BlackTheme` | Preto/cinza, sem azul |

```lua
Window:SetTheme("BlackTheme")
```

### BlackTheme

O fundo do `BlackTheme` é preto puro, então os elementos usam tons de cinza mais claros para continuar visíveis:

- **Checkbox / Radiobox**: ganham uma **borda cinza clara** (`CheckboxBorder`) e fundo mais claro que a janela, então dá para ver o box mesmo desmarcado. Marcado, o check fica branco.
- Campos (`FrameBg`), abas (`TabBg`) e regiões (`RegionBg`) estão mais claros que o fundo.

### Criando um tema próprio

```lua
ReGui:DefineTheme("MeuTema", {
    BaseTheme = ReGui.ThemeConfigs.BlackTheme, -- herda o que você não definir
    Text = Color3.fromRGB(255, 255, 255),
    FrameBg = Color3.fromRGB(40, 40, 40),
    CheckboxBorder = Color3.fromRGB(200, 200, 200),
    CheckboxBorderTransparency = 0,
})

Window:SetTheme("MeuTema")
```

### Chaves de tema mais usadas

| Chave | Afeta |
|---|---|
| `Text`, `TextDisabled` | Cor do texto |
| `WindowBg`, `WindowBgTransparency` | Fundo da janela |
| `TitleBarBg`, `TitleBarBgActive`, `TitleBarBgCollapsed` | Barra de título |
| `FrameBg`, `FrameBgActive` | Campos, sliders, checkbox |
| `ButtonsBg` | Botões |
| `CheckMark` | Cor do check marcado |
| `CheckboxBorder`, `CheckboxBorderTransparency` | Borda do checkbox/radiobox |
| `SliderGrab` | Alça do slider |
| `TabBg`, `TabBgActive`, `TabText`, `TabTextActive` | Abas |
| `RegionBg`, `MenuBar` | Regiões e barra de menu |
| `Border`, `BorderTransparency` | Borda da janela |

---

## 6. SetUI — transparência do fundo

`ReGui.SetUI` controla a aparência global da UI. Por enquanto expõe `Transparent`.

```lua
local SetUI = ReGui.SetUI

SetUI:Transparent("0.7")   -- fundo 70% transparente
SetUI:Transparent(0.3)     -- também aceita número
SetUI:Transparent(nil)     -- volta ao valor do tema ("reset" também funciona)

print(SetUI:GetTransparent())  --> nil ou o valor atual
```

**Como funciona**

- O valor vai de `0` (opaco) até `1` (totalmente transparente). Aceita string ou número e é limitado a esse intervalo.
- Afeta apenas os **fundos**: janela, barra de título (normal, ativa e recolhida), barra de menu e regiões. Botões, campos e textos continuam legíveis.
- Vale para todas as janelas **já abertas e para as criadas depois**.
- Sobrescreve a transparência de fundo de qualquer tema até você chamar `SetUI:Transparent(nil)`.

---

## 7. Dicas

- Se a janela tiver só um elemento com `Fill`, use `NoScroll = true`.
- `NoBackground = true` remove só o fundo da janela atual; `SetUI:Transparent` afeta todas.
- Ative `ReGui.Debug = true` para ver avisos de cor/tema inexistente.
