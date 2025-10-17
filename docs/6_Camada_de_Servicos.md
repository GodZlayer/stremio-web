# Camada de Serviços

A camada de serviços é uma parte fundamental da arquitetura do Stremio. Ela abstrai a lógica de negócios, o gerenciamento de estado e a comunicação com o "mundo exterior" (seja o shell do desktop, a API do Stremio ou dispositivos Chromecast) dos componentes de UI.

Essa camada é inicializada no componente `App.js` e disponibilizada para toda a árvore de componentes através do `ServicesProvider`.

## `stremio-core-web` (O Serviço `Core`)

O serviço mais importante é o `Core`, uma instância de `@stremio/stremio-core-web`. Ele atua como o cérebro da aplicação e é responsável por:

*   **Gerenciamento de Estado**: Mantém todo o estado da aplicação, incluindo perfil do usuário, biblioteca, addons instalados, catálogo de mídia, etc.
*   **Lógica de Negócios**: Executa todas as ações principais, como autenticação, busca, sincronização da biblioteca e obtenção de streams.
*   **Comunicação com a API**: Interage com a API do Stremio para buscar dados e sincronizar o estado do usuário.
*   **Sistema de Addons**: Carrega e gerencia os addons, que são a principal fonte de conteúdo no Stremio.

Os componentes da UI não modificam o estado diretamente. Em vez disso, eles despacham ações para o serviço `Core` (ex: `core.transport.dispatch(...)`) e reagem a atualizações de estado que são emitidas pelo `Core`.

## O Serviço `Shell`

O serviço `Shell` atua como uma ponte entre a aplicação web e o ambiente em que ela está rodando, especialmente quando se trata de um aplicativo de desktop (usando Electron, por exemplo). Suas responsabilidades incluem:

*   Gerenciar o estado da janela (foco, fechamento).
*   Receber eventos do ambiente externo, como a tentativa de abrir um link `stremio://` (`open-media`).
*   Enviar comandos para o shell, como `shell.send('quit')` para fechar a aplicação.

## Outros Serviços

*   **`Chromecast`**: Gerencia a descoberta e a comunicação com dispositivos Google Chromecast, permitindo que o usuário transmita vídeos para a TV.
*   **`KeyboardShortcuts`**: Registra e manipula atalhos de teclado globais para ações como play/pause, avançar, etc.
*   **`DragAndDrop`**: Habilita a funcionalidade de arrastar e soltar, usada, por exemplo, para instalar addons a partir de um arquivo ou URL.

Essa arquitetura de serviços permite uma clara separação de responsabilidades, tornando o código mais organizado, testável e fácil de manter. A UI se preocupa apenas em exibir o estado e despachar ações, enquanto a camada de serviços cuida de toda a "mágica" por trás dos panos.