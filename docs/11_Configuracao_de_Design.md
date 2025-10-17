# 11. Configuração de Design

A aplicação Stremio permite que os usuários personalizem certos aspectos visuais para melhorar sua experiência de visualização, principalmente relacionados às legendas no player de vídeo. Essas configurações são gerenciadas através da página de "Configurações" (`/settings`) e são salvas no perfil do usuário, sendo sincronizadas entre dispositivos.

## Opções de Configuração

As configurações de design estão contidas no objeto `settings` dentro do modelo de dados `Ctx` (o contexto do usuário). As principais opções de personalização visual são:

*   **`subtitlesBackgroundColor`**: Define a cor de fundo da caixa de legendas (ex: `rgba(0,0,0,0.5)`).
*   **`subtitlesBold`**: Um booleano para aplicar ou não o estilo de negrito ao texto da legenda.
*   **`subtitlesFont`**: Permite ao usuário escolher uma fonte específica para as legendas.
*   **`subtitlesOffset`**: Define o deslocamento vertical das legendas na tela.
*   **`subtitlesOutlineColor`**: Define a cor do contorno do texto da legenda, melhorando a legibilidade.
*   **`subtitlesSize`**: Controla o tamanho da fonte das legendas.
*   **`subtitlesTextColor`**: Define a cor principal do texto da legenda.

Além das legendas, a configuração `interfaceLanguage` também afeta o design, pois carrega o conjunto de traduções apropriado para toda a interface do usuário.

## Fluxo de Alteração

O processo para alterar uma configuração de design segue o fluxo de dados padrão da aplicação:

1.  **View (Interface)**: O usuário interage com um controle na página de Configurações, como um seletor de cores ou um controle deslizante para o tamanho da fonte.

2.  **Dispatch (Ação)**: Ao alterar um valor, o componente de UI despacha uma ação `UpdateSettings` para o serviço `Core`.

    ```javascript
    // Exemplo conceitual de despacho de ação
    services.core.transport.dispatch({
        action: 'Ctx',
        args: {
            action: 'UpdateSettings',
            args: {
                // Apenas as configurações que mudaram
                subtitlesSize: 120,
                subtitlesTextColor: '#FFFF00'
            }
        }
    });
    ```

3.  **Core (Processamento)**: O `Core` recebe a ação, atualiza seu estado interno com as novas configurações e as persiste na conta do usuário através da API do Stremio.

4.  **State (Atualização de Estado)**: O objeto `profile.settings` no `Core` é atualizado.

5.  **View (Re-renderização)**:
    *   Componentes que dependem dessas configurações (principalmente o `Player`) são notificados da mudança.
    *   O componente do Player, por exemplo, se re-renderiza aplicando os novos estilos (tamanho, cor, fundo) às legendas exibidas sobre o vídeo.

Este mecanismo garante que as personalizações do usuário sejam aplicadas em tempo real e mantidas de forma consistente em sua conta.