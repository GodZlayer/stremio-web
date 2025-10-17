# Componente Principal (App.js)

O arquivo `src/App/App.js` contém o componente React principal da aplicação. Ele é responsável por orquestrar os serviços, o roteamento e a renderização dos componentes de alto nível.

## Principais Responsabilidades

1.  **Inicialização de Serviços**:
    *   No hook `useEffect`, vários serviços essenciais são inicializados e iniciados:
        *   `Core`: O cérebro da aplicação, lidando com a lógica de negócios e o estado (gerenciado por `stremio-core-web`).
        *   `Shell`: Lida com a integração com o ambiente de "shell" (por exemplo, um aplicativo de desktop), gerenciando eventos como `open-media`.
        *   `Chromecast`: Gerencia a integração com o Google Chromecast.
        *   `KeyboardShortcuts`: Configura atalhos de teclado globais.
        *   `DragAndDrop`: Habilita a funcionalidade de arrastar e soltar para, por exemplo, instalar addons.
    *   O estado de inicialização (`initialized`) aguarda que os serviços `Core` e `Shell` estejam ativos (ou tenham falhado) antes de renderizar a aplicação principal.

2.  **Gerenciamento de Estado Global**:
    *   O componente usa o `ServicesProvider` para disponibilizar a instância dos serviços para todos os componentes filhos através do contexto do React.
    *   Ele escuta eventos do `stremio-core-web` para reagir a mudanças de estado, como a atualização das configurações do usuário (`SettingsUpdated`), e sincroniza o idioma da interface com `i18n`.

3.  **Roteamento**:
    *   Utiliza o componente `Router` (envolvido por `withCoreSuspender` e `withProtectedRoutes`) para gerenciar as rotas da aplicação.
    *   A configuração das rotas (`routerViewsConfig`) define qual componente de página renderizar para cada URL.
    *   `withProtectedRoutes` garante que certas rotas só sejam acessíveis após o `Core` ser totalmente inicializado e o usuário estar autenticado.

4.  **Renderização Condicional**:
    *   Enquanto os serviços estão inicializando, um indicador de carregamento (`loader-container`) é exibido.
    *   Se ocorrer um erro fatal na inicialização do `Core`, um diálogo de erro (`ErrorDialog`) é mostrado.
    *   Após a inicialização bem-sucedida, a aplicação principal é renderizada.

5.  **Context Providers**:
    *   A aplicação é envolvida por vários *providers* de contexto que oferecem funcionalidades globais:
        *   `PlatformProvider`: Fornece informações sobre a plataforma atual.
        *   `ToastProvider`: Gerencia a exibição de notificações (toasts).
        *   `TooltipProvider`: Gerencia a exibição de tooltips.
        *   `FileDropProvider`: Habilita uma área para arrastar e soltar arquivos.

Em resumo, `App.js` atua como o maestro da orquestra, garantindo que todos os serviços estejam funcionando, o estado esteja sincronizado e os componentes corretos sejam renderizados com base na URL e no estado da aplicação.