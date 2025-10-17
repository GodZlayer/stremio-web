# Fluxo de Dados e Gerenciamento de Estado

A aplicação Stremio adota um padrão de fluxo de dados unidirecional, inspirado em arquiteturas como Flux e Redux. Isso garante que o fluxo de dados seja previsível e fácil de depurar. A camada de serviços, especialmente o `Core`, é a única fonte de verdade para o estado da aplicação.

O ciclo de vida de uma atualização de dados funciona da seguinte maneira:

## 1. Ação do Usuário (View)

Tudo começa na interface do usuário (a "View"). Um usuário interage com um componente React, por exemplo, clicando em um botão para adicionar um filme à biblioteca.

## 2. Despacho da Ação (Dispatch)

O componente não modifica o estado diretamente. Em vez disso, ele despacha uma **ação** para o serviço `Core`. As ações são objetos que descrevem *o que* aconteceu e carregam os dados necessários para a alteração.

O despacho é feito através do `transport` do `Core`:

```javascript
// Exemplo dentro de um componente
const { services } = useServices();

const addToLibrary = () => {
    services.core.transport.dispatch({
        action: 'Ctx', // Ação para o contexto do usuário
        args: {
            action: 'AddToLibrary',
            args: [/* meta item para adicionar */]
        }
    });
};
```

## 3. Processamento da Ação (Core)

O serviço `Core` (`stremio-core-web`) recebe a ação. Ele contém toda a lógica de negócios para processá-la. Isso pode envolver:

*   Fazer uma chamada para a API externa do Stremio.
*   Modificar o estado interno (por exemplo, adicionar o item à lista da biblioteca).
*   Lidar com a lógica de addons para buscar mais dados.

O `Core` é o único lugar onde o estado da aplicação pode ser modificado.

## 4. Atualização do Estado (State)

Após processar a ação, o `Core` atualiza seu estado interno. Em seguida, ele emite um evento para notificar que o estado mudou.

## 5. Re-renderização da View (View)

Os componentes React que dependem dessa parte do estado precisam ser notificados para que possam se re-renderizar com os novos dados. Isso é geralmente feito através de hooks customizados que "escutam" as mudanças no estado do `Core`.

O hook `useModelState` (localizado em `src/common/useModelState.js`) é um bom exemplo. Ele permite que um componente se inscreva em uma parte específica do estado do `Core`.

```javascript
// Exemplo de uso do hook para obter dados da biblioteca
import { useModelState } from 'stremio/common';

const LibraryComponent = () => {
    // Inscreve-se no modelo 'Library' do Core
    const { model, ...state } = useModelState({ model: 'Library' });

    if (state.loading) {
        return <p>Carregando...</p>;
    }

    return (
        <ul>
            {state.catalog.map(item => (
                <li key={item._id}>{item.name}</li>
            ))}
        </ul>
    );
};
```

Quando o `Core` atualiza o estado da `Library`, o hook `useModelState` recebe a notificação, obtém os novos dados e causa a re-renderização do `LibraryComponent` com a lista atualizada.

Este ciclo **Ação -> Dispatch -> Core -> Estado -> View** garante que o estado seja consistente em toda a aplicação e que as atualizações de UI sejam uma consequência direta das mudanças de estado, nunca a causa.