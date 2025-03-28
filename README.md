# Projeto de Chatbot com LangChain e Groq

## Índice
1. [Introdução](#introducao)
2. [Tecnologias Utilizadas](#tecnologias-utilizadas)
3. [Instalação](#instalacao)
4. [Explicação do Código](#explicacao-do-codigo)
   - [Importação das Bibliotecas](#importacao-das-bibliotecas)
   - [Carregamento das Variáveis de Ambiente](#carregamento-das-variaveis-de-ambiente)
   - [Inicialização do Modelo de IA](#inicializacao-do-modelo-de-ia)
   - [Gerenciamento de Sessões e Histórico](#gerenciamento-de-sessoes-e-historico)
   - [Criação do Prompt Template](#criacao-do-prompt-template)
   - [Otimização e Limitação do Contexto](#otimizacao-e-limitacao-do-contexto)
5. [Glossário](#glossario)
6. [Como Clonar e Executar o Projeto](#como-clonar-e-executar-o-projeto)

## Introdução <a id="introducao"></a>
Este projeto implementa um chatbot utilizando LangChain e a API do Groq. Ele permite a interação contextual com memória de conversação e otimização de tokens para respostas eficientes.

## Tecnologias Utilizadas <a id="tecnologias-utilizadas"></a>
- Python
- LangChain
- Groq API
- dotenv
- Operator

## Instalação <a id="instalacao"></a>
1. Clone o repositório:
   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio
   ```
2. Crie e ative um ambiente virtual (opcional, mas recomendado):
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/macOS
   venv\Scripts\activate  # Windows
   ```
3. Instale as dependências:
   ```bash
   pip install -r requirements.txt
   ```
4. Execute o script:
   ```bash
   python seu_script.py
   ```

## Explicação do Código <a id="explicacao-do-codigo"></a>

### Importação das Bibliotecas <a id="importacao-das-bibliotecas"></a>
Importamos todas as bibliotecas necessárias para manipulação de IA, armazenamento de contexto e execução do chatbot.
```python
import os
from dotenv import load_dotenv
from langchain_groq import ChatGroq
from langchain_community.chat_message_histories import ChatMessageHistory
from langchain_core.chat_history import BaseChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.messages import HumanMessage, AIMessage, SystemMessage, trim_messages
from langchain_core.runnables import RunnablePassthrough
from operator import itemgetter
```

### Carregamento das Variáveis de Ambiente <a id="carregamento-das-variaveis-de-ambiente"></a>
Carregamos as variáveis do `.env` para manter credenciais seguras.
```python
load_dotenv()
groq_api_key = os.getenv("GROQ_API_KEY")
```

### Inicialização do Modelo de IA <a id="inicializacao-do-modelo-de-ia"></a>
Criamos a instância do modelo `gemma2-9b-it` com a API do Groq.
```python
model = ChatGroq(model="gemma2-9b-it", groq_api_key=groq_api_key)
```

### Gerenciamento de Sessões e Histórico <a id="gerenciamento-de-sessoes-e-historico"></a>
Mantemos um histórico por sessão para manter contexto entre mensagens.
```python
store = {}

def get_session_history(session_id: str) -> BaseChatMessageHistory:
    if session_id not in store:
        store[session_id] = ChatMessageHistory()
    return store[session_id]

with_message_history = RunnableWithMessageHistory(model, get_session_history)
config = {"configurable": {"session_id": "chat1"}}
```

### Criação do Prompt Template <a id="criacao-do-prompt-template"></a>
Estruturamos o prompt para orientar o modelo a responder adequadamente.
```python
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "Você é um assistente útil. Responda todas as perguntas usando suas habilidades em {language}."),
        MessagesPlaceholder(variable_name="messages")
    ]
)
```

### Otimização e Limitação do Contexto <a id="otimizacao-e-limitacao-do-contexto"></a>
Para manter a memória sob controle, limitamos a quantidade de tokens processados.
```python
trimmer = trim_messages(
    max_tokens=45,
    strategy="last",
    token_counter=model,
    include_system=True,
    allow_partial=False,
    start_on="human"
)
```

## Glossário <a id="glossario"></a>
- **LLM (Large Language Model)**: Modelos de IA treinados em grandes volumes de texto.
- **Prompt Engineering**: Criação de prompts eficazes para IA.
- **Chain of Thought**: Estratégia para melhorar o raciocínio dos modelos.
- **Tokens**: Unidades de texto usadas para processamento de IA.

## Como Clonar e Executar o Projeto <a id="como-clonar-e-executar-o-projeto"></a>
1. Clone o repositório:
   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio
   ```
2. Crie e ative um ambiente virtual:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/macOS
   venv\Scripts\activate  # Windows
   ```
3. Instale as dependências:
   ```bash
   pip install -r requirements.txt
   ```
4. Execute o script:
   ```bash
   python seu_script.py
   ```

Agora, seu projeto estará pronto para uso! 🚀


