# 10. Sistema de Catálogos

O sistema de catálogos é a forma como o Stremio apresenta o conteúdo para descoberta pelo usuário. Telas como o "Quadro" (`/board`) e "Descobrir" (`/discover`) são inteiramente construídas a partir de catálogos fornecidos pelos addons instalados.

## Definição

Um **catálogo** é uma lista de itens de mídia (filmes, séries, canais, etc.) fornecida por um addon. Cada addon pode declarar em seu `manifest` os catálogos que ele oferece. Por exemplo, um addon pode fornecer um catálogo de "Filmes Populares", "Séries em Alta" ou "Filmes de Ação".

## Como Funciona

O processo de exibição de catálogos segue o fluxo de dados padrão da aplicação:

1.  **Carregamento da Rota**: O usuário navega para uma rota que exibe catálogos, como a `/board`. O componente `Board.js` é renderizado.

2.  **Solicitação de Dados**: O componente `Board` (ou `Discover`) usa o hook `useModelState` para solicitar os dados do modelo correspondente do `Core` (por exemplo, o modelo `Board`).

    ```javascript
    // Exemplo conceitual de uso no componente Board
    const { model, ...boardState } = useModelState({ model: 'Board' });
    ```

3.  **Processamento no `Core`**:
    *   Ao receber a solicitação, o `Core` identifica quais addons instalados pelo usuário fornecem catálogos.
    *   Ele faz requisições HTTP para os endpoints de catálogo de cada um desses addons. Por exemplo, ele pode pedir ao addon Cinemeta o catálogo de "Filmes em Destaque".
    *   O `Core` aguarda as respostas e as agrega em uma única estrutura de dados que representa todo o conteúdo a ser exibido na tela.

4.  **Atualização do Estado**: O `Core` atualiza seu estado interno com os dados do catálogo agregados. Isso inclui as linhas de conteúdo, os metadados de cada item, etc.

5.  **Re-renderização da UI**:
    *   O hook `useModelState` no componente `Board` é notificado sobre a atualização do estado.
    *   O componente recebe os novos dados (`boardState`) e se re-renderiza, exibindo as linhas de catálogos (como "Filmes Populares") e os pôsteres dos itens de mídia dentro de cada linha.

## Agregação e Apresentação

A grande força do Stremio está na **agregação**. A aplicação não se importa *qual* addon forneceu qual item. Ela simplesmente solicita o conteúdo ao `Core`, e o `Core` orquestra a comunicação com todos os addons relevantes para construir a página.

Isso significa que a tela "Descobrir" de um usuário pode ser completamente diferente da de outro, dependendo exclusivamente dos addons que cada um instalou. Se um usuário instala um addon que fornece um catálogo de "Animes da Temporada", essa linha aparecerá em sua interface sem que a aplicação precise de qualquer modificação.