# Inicialização da Aplicação

O ponto de entrada da aplicação é o arquivo `src/index.js`. Ele é responsável por configurar o ambiente e renderizar o componente principal do React. O processo de inicialização segue os seguintes passos:

1.  **Monitoramento de Erros**: Se uma chave de Sentry (`SENTRY_DSN`) estiver definida nas variáveis de ambiente, o Sentry é inicializado para capturar e reportar erros em produção.

2.  **Ajuste de Viewport**: A biblioteca `bowser` é usada para detectar o tipo de dispositivo. Se a aplicação estiver rodando em um desktop, a meta tag `viewport` é ajustada para otimizar a visualização.

3.  **Internacionalização (i18n)**: A biblioteca `i18next` é configurada com as traduções fornecidas pelo pacote `stremio-translations`. Isso permite que a interface do usuário seja exibida em múltiplos idiomas.

4.  **Renderização do React**: O `ReactDOM.createRoot` é usado para criar a raiz da aplicação React no elemento HTML com o ID `app`. Em seguida, o componente principal, `<App />` (localizado em `src/App/index.js`), é renderizado dentro dessa raiz.

5.  **Registro do Service Worker**: Em ambiente de produção, um *service worker* (`service-worker.js`) é registrado. Isso habilita funcionalidades de Progressive Web App (PWA), como cache de recursos para acesso offline e melhor desempenho.

O componente `<App />` assume o controle a partir deste ponto, gerenciando o estado, as rotas e a renderização de toda a interface da aplicação.