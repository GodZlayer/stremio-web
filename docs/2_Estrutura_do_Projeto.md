# Estrutura do Projeto

O código-fonte da aplicação está localizado no diretório `src` e é organizado da seguinte forma:

*   **`src/App`**: Contém a lógica principal da aplicação, incluindo o componente raiz, gerenciamento de estado global e inicialização.

*   **`src/common`**: Armazena utilitários, hooks e componentes reutilizáveis que são genéricos e podem ser usados em qualquer parte da aplicação.

*   **`src/components`**: Um diretório para componentes de UI reutilizáveis que são mais específicos do domínio da aplicação do que os de `common`. Por exemplo, `MetaItem` ou `LibItem`.

*   **`src/router`**: Contém a configuração do roteamento da aplicação, definindo as URLs e os componentes que devem ser renderizados para cada uma.

*   **`src/routes`**: Contém os componentes de página que são renderizados pelo roteador. Cada arquivo aqui corresponde a uma rota principal da aplicação (ex: "Descobrir", "Biblioteca").

*   **`src/services`**: Centraliza a comunicação com APIs externas e o *core* do Stremio (`stremio-core-web`). Abstrai a lógica de busca e manipulação de dados.

*   **`src/types`**: Define os tipos de dados usados em toda a aplicação, principalmente com TypeScript. Isso ajuda a garantir a consistência e a prevenir erros.

*   **`src/index.html`**: O template HTML principal da aplicação.

*   **`src/index.js`**: O ponto de entrada da aplicação, onde o React é inicializado e o componente `App` é renderizado no DOM.