# 9. Sistema de Addons (Extensões)

O sistema de addons é o que torna o Stremio uma plataforma de mídia agregadora e extensível. Os addons são a única fonte de conteúdo (catálogos de filmes, séries, canais e os próprios streams de vídeo). A aplicação em si não contém nenhum conteúdo; ela apenas descobre e apresenta o conteúdo fornecido pelos addons que o usuário instala.

## O que são Addons?

Um addon é, essencialmente, um serviço web que segue a [especificação de addons do Stremio](https://github.com/Stremio/stremio-addon-sdk/blob/master/docs/api.md). Ele expõe um `manifest.json` que descreve o que o addon oferece (tipos de conteúdo, catálogos) e implementa endpoints para fornecer esses dados.

## Instalação e Gerenciamento

A interface para gerenciar addons está na rota `/addons` (componente `src/routes/Addons`).

O fluxo de instalação é o seguinte:

1.  **Descoberta**: O usuário pode encontrar addons em uma lista de addons oficiais ou instalar um addon de terceiros através de uma URL.
2.  **Ação de Instalação**: Ao clicar em "Instalar", a UI despacha uma ação para o `Core`, como `InstallAddon`.
3.  **Atualização de Estado**: O `Core` atualiza seu estado, adicionando o `manifest` do addon à lista de addons instalados (`profile.addons`). Esta lista é sincronizada com a conta do usuário, tornando os addons disponíveis em todos os seus dispositivos.

É importante notar que a "instalação" na aplicação web é simplesmente o ato de registrar a URL do `manifest` do addon no perfil do usuário. A aplicação não hospeda ou executa o código do addon.

## Fornecimento de Conteúdo

Uma vez que um addon é instalado, o `Core` passa a se comunicar com ele para obter dados.

1.  **Catálogos**:
    *   Quando o usuário navega para uma página como "Descobrir" (`/discover`), o `Core` consulta os addons instalados que declararam fornecer catálogos.
    *   Ele faz requisições HTTP para os endpoints de catálogo de cada addon relevante (ex: `https://v3-cinemeta.strem.io/catalog/movie/top.json`).
    *   O `Core` agrega as respostas de todos os addons e as apresenta na UI.

2.  **Streams de Vídeo**:
    *   Quando o usuário está na página de detalhes de um filme ou episódio (`/metadetails`) e clica para assistir, o `Core` realiza a etapa mais crucial: a busca por streams.
    *   Ele envia uma requisição para o endpoint de *streams* de todos os addons instalados que suportam aquele tipo de conteúdo (ex: `https://v3-cinemeta.strem.io/stream/movie/tt123456.json`).
    *   Cada addon responde com uma lista de possíveis fontes de vídeo (streams).
    *   O `Core` agrega todas as respostas, as organiza e as exibe para o usuário. Ao selecionar um stream, o usuário é direcionado para a rota do player (`/player`).

Essa arquitetura permite que a aplicação Stremio seja apenas um "shell" ou um "navegador" de conteúdo, com a lógica de busca e fornecimento de mídia completamente delegada aos addons.