# 8. Autenticação (Login e Registro)

O sistema de autenticação é central para a experiência do Stremio, pois permite a sincronização de biblioteca, configurações e histórico entre dispositivos. Todo o processo é orquestrado pelo serviço `Core` (`stremio-core-web`), que se comunica com o servidor de autenticação do Stremio.

## Fluxo de Dados

O fluxo segue o padrão de dados unidirecional da aplicação:

1.  **View (Interface)**: O usuário insere suas credenciais (email e senha) nos componentes de UI, geralmente localizados na rota `/intro` ou em um modal de login.
2.  **Dispatch (Ação)**: Ao submeter o formulário, o componente despacha uma ação para o serviço `Core`, como `Login` ou `Register`.

    ```javascript
    // Exemplo conceitual de despacho de ação de login
    services.core.transport.dispatch({
        action: 'Ctx',
        args: {
            action: 'Login',
            args: {
                email: 'user@example.com',
                password: 'password123'
            }
        }
    });
    ```

3.  **Core (Processamento)**:
    *   O `Core` recebe a ação e envia uma requisição para o servidor de autenticação do Stremio.
    *   Ele lida com a resposta do servidor. Em caso de sucesso, o `Core` recebe um token de autenticação e os dados do perfil do usuário.
    *   Em caso de falha (ex: senha incorreta), ele armazena o erro.

4.  **State (Atualização de Estado)**:
    *   Se o login for bem-sucedido, o `Core` atualiza seu estado interno (`Ctx`), preenchendo o `profile` com os dados do usuário e armazenando o token de autenticação.
    *   O estado de autenticação (`auth.user`) passa a conter as informações do usuário.

5.  **View (Re-renderização)**:
    *   Componentes inscritos no estado do `Ctx` (usando `useModelState` ou similar) são notificados da mudança.
    *   O `withProtectedRoutes` detecta que o usuário está autenticado e permite o acesso às rotas protegidas, como a `/board` ou `/library`.
    *   A UI é atualizada para refletir o estado de "logado", exibindo o avatar do usuário e outras informações personalizadas.

## Servidor de Autenticação

A aplicação web não implementa a lógica do servidor de autenticação. Ela atua como um cliente para a API do Stremio. O `stremio-core-web` abstrai completamente essa comunicação, de modo que os componentes da UI não precisam saber os detalhes dos endpoints da API.

O registro de novos usuários segue um fluxo quase idêntico, utilizando uma ação `Register` em vez de `Login`.