# Conceitos básicos de React.js
**Um simples front-end que faz requisições como GET, POST e DELETE para nossa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api")**

Antes de começar este tutorial é de suma importancia que você já tenha a nossa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api") clonada e rodando em seu ambiente de desenvolvimento, pois criaremos um fron-end que consumirá os dados dessa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api").

---
## Rodando o projeto
````git
$ git clone https://github.com/AdeLuigi/conceitos-basicos-reactjs.git
$ cd conceitos-basicos-reactjs
$ yarn
````

**Neste momento o servidor já deve estar pronto para rodar**
````git
$ yarn start
````

## O que você verá implementado?
* Uso da biblioteca Axios para nos auxiliar a fazer requisções para a nossa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api").
* Utilização dos React Hooks como useState e useEffect.
* Listagem, exclusão e adição de dados em nossa api.
---

## A aplicação
````javascript
import React, { useState, useEffect } from "react";
import api from './services/api';

import "./styles.css";

function App() {

  const [ repositories, setRepositories ] = useState([]);

  useEffect(() => {
    
    api.get('repositories').then(response => {
      setRepositories(response.data)
    })

  }, []);

  async function handleAddRepository() {

    const response = await api.post('repositories',{
      "title": `Front-end com React.JS ${Date.now()}`,
      "url": "ademario.com.br",
      "techs": ["1", "2", "3"]
    });
    setRepositories([...repositories,response.data])
  }

  async function handleRemoveRepository(id) {

    const response = await api.delete(`repositories/${id}`);

    if(response.status === 204){
      const repositoriesNotDeleted = repositories.filter((repository) => {
        return repository.id !== id
      })
      setRepositories(repositoriesNotDeleted);
    }
  }

  return (
    <div>
      <ul data-testid="repository-list">
        {repositories.map((repository)=> (        
          <li key={repository.id}>
            {repository.title}

            <button onClick={() => handleRemoveRepository(repository.id)}>
              Remover
            </button>
          </li>))

        }
      </ul>

      <button onClick={handleAddRepository}>Adicionar</button>
    </div>
  );
}

export default App;
````
Como diria Jack o Estripador, vamos por partes. A primeira linha representa os imports dos Hooks (useStete e useEffect), logo em seguida nós importamos a função de acesso à api e por ultimo nós atribuímos um arquivo css à nossa página.

````javascript
import React, { useState, useEffect } from "react";
import api from './services/api';
import "./styles.css";
````
Com todos esses imports nós estamos prontos para criar o nosso component App e para fazer a requisição de listagem dos nossos repositórios.
````javascript
function App() {
const [ repositories, setRepositories ] = useState([]);

````
Uma boa prática pra a utilização do Hook useState é desestrutura ele e foi isso que nós fizemos, a variável **repositories** armazena todos os dados atuais do nosso array de repositórios e variável **setRepositories** é utilizada em forma de função para alterar o valor de **repositories** indiretamente. Esse conceito de mudança indireta de valor parece estranho, mas para utilizarmos de forma correta os Hooks nós precisamos respeitar o conceito de imutabilidade.


````javascript
  useEffect(() => {
    
    api.get('repositories').then(response => {
      setRepositories(response.data)
    })

  }, []);
````
Depois de desestruturar a nossa função useState, nós utilizamos outro Hook chamado **useEffect**, ele é reponsável por receber uma função como primeiro parâmetro e uma **variável** como segundo, monitorar esta **variável** e disparar funções assim que o valor dela for alterado, mas como nós não passamos uma **variável** para ser monitorada o useEffect só será acionado uma vez durante o carregamento da página, isso nos auxiliará na hora de fazer a listagem dos repositórios.

Logo em seguida fazemos uma requisição do tipo get para a rota 'repositories' e alteramos o valor atual da variável repositories com a chamada da função **setRepositories()**, passando como parâmetro o valor do **response.data** que corresponde a todos os nossos repositórios no banco de dados.
````javascript
async function handleAddRepository() {

    const response = await api.post('repositories',{
        "title": `Front-end com React.JS ${Date.now()}`,
        "url": "ademario.com.br",
        "techs": ["1", "2", "3"]
    });
    setRepositories([...repositories,response.data])
}
````
Aqui nós criamos uma função assíncrona  **handleAddRepository** que é responsável por adicionar novos repositórios em nosso banco de dados, a variável **response** é responsável por armazenar a resposta da nossa requisição, ela armazenará o repositório que acabamos de adicionar.

Tendo em vista isso, nós podemos utilizar o **setRepositories()** para alterar indiretamente o valor de repositories, o primeiro parâmetro que nós passamos para **setRepositories()** é o valor atual de repositories e o segundo é o valor que será adicionado ao final do array.

````javascript
async function handleRemoveRepository(id) {

const response = await api.delete(`repositories/${id}`);

    if(response.status === 204){
        const repositoriesNotDeleted = repositories.filter((repository) => {
        return repository.id !== id
        })
        setRepositories(repositoriesNotDeleted);
    }
}

````
Aqui nós criamos uma função assíncrona  **handleRemoveRepository** que recebe um id como parâmetro e é responsável por deletar o repositório no nosso banco de dados correspondente aquele id. A variável response é responsável por armazenar a resposta da nossa requisição.

Tendo em vista isso, nós checamos se o status do nosso response é 204 (204 corresponde a um delete feito com sucesso) e passamos todos os repositórios com id diferente do que foi excluído para a variável **repositoriesNotDeleted** e usamos **setRepositories(repositoriesNotDeleted)** para alterar o valor da variável **repositories** indiretamente.

````javascript
  return (
    <div>
      <ul data-testid="repository-list">
        {repositories.map((repository)=> (        
          <li key={repository.id}>
            {repository.title}

            <button onClick={() => handleRemoveRepository(repository.id)}>
              Remover
            </button>
          </li>))

        }
      </ul>

      <button onClick={handleAddRepository}>Adicionar</button>
    </div>
  );
````
O **return** contém todo o conteúdo que será mostrado no component, utilizamos o **repositories.map()** para percorrer o nosso array de repositórios e renderizar cada repositório que foi encontrado.