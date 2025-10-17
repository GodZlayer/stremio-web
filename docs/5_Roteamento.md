# Roteamento

O roteamento na aplicação Stremio é gerenciado por uma combinação do componente `Router` e um arquivo de configuração centralizado, o `src/App/routerViewsConfig.js`. Este arquivo define quais componentes de página devem ser renderizados com base na URL atual.

## Configuração das Rotas (`routerViewsConfig.js`)

O `routerViewsConfig.js` exporta um array de arrays, onde cada array interno representa um "nível" ou "grupo" de rotas na interface. O roteador processa essa estrutura para mapear padrões de URL a componentes React.

A estrutura é a seguinte:

```javascript
const routerViewsConfig = [
    [
        { regexp: /^\/board$/, component: routes.Board }
    ],
    [
        { regexp: /^\/discover\/(.*)/, component: routes.Discover },
        { regexp: /^\/library/, component: routes.Library },
        // ... outras rotas
    ],
    // ... outros níveis de rotas
];
```

*   **`regexp`**: Uma expressão regular que define o padrão da URL para a rota. Essas expressões vêm do módulo `stremio/common` (`routesRegexp`).
*   **`component`**: O componente React (localizado em `src/routes`) que será renderizado quando a URL corresponder à `regexp`.

## Principais Rotas da Aplicação

Com base na configuração, as principais rotas incluem:

*   `/board`: O "Quadro", a tela inicial principal.
*   `/intro`: Uma página de introdução para novos usuários.
*   `/discover/...`: A seção "Descobrir" para encontrar novo conteúdo.
*   `/library`: A biblioteca de mídia do usuário.
*   `/calendar`: Um calendário de lançamentos.
*   `/continuewatching`: A lista de "Continue Assistindo".
*   `/search`: A página de busca.
*   `/metadetails/...`: A página de detalhes de um item de mídia (filme, série).
*   `/addons/...`: A seção de gerenciamento de addons.
*   `/settings`: A página de configurações.
*   `/player/...`: O player de vídeo.

## Funcionamento

O componente `Router`, que recebe esta configuração, monitora as mudanças na URL (`window.location.hash`). Quando a URL muda, ele itera sobre o `routerViewsConfig`, encontra a primeira `regexp` que corresponde à nova URL e renderiza o `component` associado a ela.

O `withProtectedRoutes` garante que, antes de renderizar a maioria dessas rotas, a aplicação verifique se o `stremio-core-web` está pronto e se o usuário está logado, redirecionando para a página de introdução (`/intro`) caso contrário.