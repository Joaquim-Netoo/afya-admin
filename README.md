# Afya Admin — Dashboard com Blazor WebAssembly e MudBlazor

## Identificação

| | |
|---|---|
| **Aluno(a)** | Joaquim Camillo |
| **Matrícula** | _preencher_ |
| **Faculdade** | _preencher_ |
| **Curso** | _preencher_ |
| **Disciplina** | _preencher_ |
| **Professor(a)** | _preencher_ |
| **Semestre** | 2026.2 |


## Objetivo do projeto

O projeto é um painel administrativo (dashboard) da plataforma fictícia "Afya Pedagógico", feito com Blazor WebAssembly e MudBlazor seguindo o tutorial da disciplina. A ideia é praticar a construção de uma interface completa só com componentes, sem escrever CSS.

A página tem um sidebar com o menu, uma AppBar com busca, troca de tema, notificações e usuário, e o conteúdo do dashboard: 4 indicadores com mini gráficos, um gráfico de linha de receita x meta, um gráfico de rosca de clientes, a performance dos projetos, as atividades recentes e uma tabela de projetos. Todos os dados são fictícios e ficam em `Data/DashboardData.cs`.

## O que aprendi

**1. Como a aplicação inicia** (ver `wwwroot/index.html` e `Program.cs`)

O navegador abre o `wwwroot/index.html`, que é a única página HTML de verdade. Nele existe a `<div id="app">` com a animação de carregamento e o script `_framework/blazor.webassembly.js`, que baixa o runtime do .NET em WebAssembly e as DLLs do projeto. O runtime executa o `Program.cs`, que registra os serviços (`AddMudServices`) e, com `RootComponents.Add<App>("#app")`, coloca o componente `App` dentro da div, no lugar do carregamento. O `App.razor` tem o roteador, que olha a URL e mostra a página certa dentro do `MainLayout`.

**2. Layout, Page e Component** (ver `Layout/MainLayout.razor`, `Pages/Dashboard.razor`, `Components/KpiCard.razor`)

O Layout é a moldura que aparece em todas as páginas: o `MainLayout.razor` tem a AppBar, o sidebar e o `@Body`. A Page é um componente com rota: o `Dashboard.razor` tem `@page "/"` e é renderizado no lugar do `@Body`. O Component é uma peça reutilizável sem rota, que recebe dados por parâmetro, como o `KpiCard`, usado 4 vezes no `Dashboard.razor`.

**3. RenderFragment e o DashboardCard** (ver `Components/DashboardCard.razor` e quem o usa)

`RenderFragment` é um parâmetro que recebe um pedaço de marcação em vez de um valor. O `DashboardCard` tem três: `Acoes`, `Menu` e `ChildContent`. O `GraficoReceita` preenche `Acoes` com a legenda e `Menu` com os itens do "⋮"; o `ProjetosRecentes` põe o botão "Ver todos" em `Acoes` e não passa `Menu`, e por isso o "⋮" nem aparece (`@if (Menu is not null)`). A estrutura do card fica escrita uma vez só.

**4. @bind-Valor e ValorChanged** (ver `Components/SeletorPeriodo.razor`)

O Blazor tem a convenção de que um parâmetro `Valor` junto com um `EventCallback` chamado `ValorChanged` permite escrever `@bind-Valor`. No `Dashboard.razor` usei `@bind-Valor="_periodo"`: o Blazor passa `_periodo` para dentro do componente e, quando clico numa opção, o `SelecionarAsync` chama `ValorChanged.InvokeAsync(opcao)`, que atualiza o `_periodo` da página. O seletor não muda o próprio `Valor`; ele só avisa, e quem é dono do estado é a página.

**5. Por que a pasta Data** (ver `Data/DashboardData.cs`)

A pasta `Data` diz o que mostrar (records como `Kpi` e `ProjetoRecente` e as listas fake) e os componentes dizem como mostrar. Como cada componente recebe os dados por parâmetro, por exemplo `<KpiCard Kpi="kpi" />`, se os dados vierem de uma API eu só troco a origem, e os componentes continuam iguais.

**6. MudGrid com xs, sm e lg** (ver o `MudGrid` em `Pages/Dashboard.razor`)

O `MudGrid` divide a largura em 12 colunas e cada `MudItem` diz quantas ocupa em cada tamanho de tela. Com `xs="12" sm="6" lg="3"`, no celular (menos de 600px) cada KPI ocupa as 12 colunas e fica 1 por linha; a partir de 600px ocupa 6 e ficam 2 por linha; a partir de 1280px ocupa 3 e ficam os 4 lado a lado. Cada valor vale daquele tamanho para cima.

**7. Estilizar sem CSS** (ver o `_theme` em `Layout/MainLayout.razor` e `Components/Ui.cs`)

Usei três coisas: os parâmetros dos componentes (`Elevation`, `Variant`, `Color`, `Size`), o tema e as classes utilitárias do MudBlazor. O `MudTheme` fica no `MainLayout.razor` e define as paletas clara e escura, o arredondamento, a altura da AppBar e a tipografia; os componentes leem as cores de variáveis CSS geradas pelo tema, e por isso o modo escuro funciona só trocando o `IsDarkMode`. As classes utilitárias como `pa-4`, `d-flex` e `mud-text-secondary` cuidam de espaçamento e alinhamento, e o `Ui.FundoSuave` monta a classe `mud-success-hover` para o fundo pastel dos ícones.

**8. Namespace afya_admin** (ver `Program.cs` e `_Imports.razor`)

O .NET usa o nome do projeto como namespace raiz, mas o hífen não é permitido em identificadores C#: `afya-admin` seria lido como uma subtração. O SDK troca o caractere inválido por sublinhado. Por isso a pasta e o `.csproj` se chamam `afya-admin`, e no código aparece `afya_admin`, como em `@using afya_admin.Components`.

## HTML gerado (DevTools)

O texto abaixo descreve o HTML real da aplicação. Confira se o seu print mostra o mesmo elemento antes de usar.

Inspecionei o card de KPI "Receita" e o botão "Novo Projeto". O `<MudPaper Elevation="1" Class="pa-4" Height="100%">` virou `<div class="mud-paper mud-elevation-1 pa-4" style="height:100%;">`: a classe `pa-4` que escrevi no `Class` aparece igual no HTML, junto com as que o MudBlazor adiciona. O `<MudStack Row="true" Spacing="3" AlignItems="AlignItems.Center">` virou `<div role="group" class="d-flex flex-row align-center gap-3">`. O `<MudButton>` virou um `<button type="button">` com as classes `mud-button-root mud-button mud-button-filled mud-button-filled-primary mud-button-filled-size-large`, e o ícone é um `<svg>` dentro de um `<span class="mud-button-icon-start">`.

## Dificuldades e soluções

Estes problemas aconteceram de verdade na construção deste projeto. Para ver cada correção, rode `git show <commit>` na pasta do projeto.

**1. Os menus de notificações e do usuário não abriam** (commit `2d0cab7`)

Com o código do tutorial, clicar no sino e no avatar não fazia nada, enquanto o menu de período e os "⋮" dos cards funcionavam. O template instalou o MudBlazor 9.11.0, e nessa versão o `ActivatorContent` do `MudMenu` recebe um `MenuContext` e não abre mais o menu sozinho no clique. A solução foi chamar `context.ToggleAsync` no `@onclick` do elemento ativador, no `MainLayout.razor`.

**2. O nome do usuário não sumia no celular** (commit `d798fae`)

O tutorial usa `Class="d-none d-md-flex"` no `MudStack` com o nome e o e-mail, mas no modo celular o nome continuava aparecendo e estourava a AppBar. Inspecionando o HTML vi que o `MudStack` gera a própria classe `d-flex`, e no CSS do MudBlazor a regra `.d-flex` vem depois da `.d-none`, as duas com `!important`, então a `d-flex` vence. Resolvi colocando as classes num `div` em volta (`d-none d-md-block`), sem escrever CSS.

**3. O comando `dotnet` não funcionava**

`dotnet --version` respondia "No .NET SDKs were found": a máquina tinha só o runtime do .NET 10, e não o SDK. Instalei o SDK com `winget install Microsoft.DotNet.SDK.10`.

**4. Linhas cortadas no PDF do tutorial**

Em `Data/DashboardData.cs`, as linhas dos projetos "Portal Institucional" e "Aplicativo Mobile" estavam cortadas na margem do PDF, sem o progresso e o prazo. Usei valores coerentes com o resto da tabela (72% / 30 Set e 65% / 05 Out). Se você tiver o tutorial original em Markdown, troque pelos valores de lá.

---

## O que falta você fazer

Repositório: https://github.com/Joaquim-Netoo/afya-admin (o remoto `origin` já está configurado).

1. Fazer o primeiro push no seu terminal, para entrar na conta do GitHub:
   `git -C C:\projetos\afya-admin push -u origin main`
2. Tirar o print do DevTools:
   - em `C:\projetos\afya-admin`, rodar `dotnet watch`;
   - no navegador, F12 → aba Elements → Ctrl + Shift + C → clicar no card "Receita";
   - expandir o `div.mud-paper` até aparecer o `div.d-flex.flex-row` de dentro;
   - Win + Shift + S, capturar a janela e salvar em `C:\projetos\afya-admin\docs\prints\devtools.png`.
3. No `README.md`: preencher a identificação e trocar cada `_preencher_` pelo seu texto.
4. Commit e push, depois abrir o link numa janela anônima e conferir que os 4 prints aparecem.
5. Enviar o link no Canvas.
