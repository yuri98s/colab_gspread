# colab_gspread
Você pode visualizar o notebook Jupyter diretamente no GitHub:

[Veja o notebook Jupyter](https://github.com/yuri98s/colab_gspread/blob/main/colab_gspread.ipynb)

## Explicando o código!
**BIBLIOTÉCAS**

* Esta são as bibliotécas responsáveis pela raspagem de dados web, tratamento dos dados e integração do Google Colab com o Google Planilhas.
```
#WebScraping
import pandas as pd
import requests
from bs4 import BeautifulSoup

#integrar Google Colab e Google Planilhas
from google.colab import auth
import gspread
from google.auth import default
```
___________________________________________________________________________________________________
**WEB SCRAPING**

* Definir a URL: URL = 'https://www.infomoney.com.br/ferramentas/cambio/'<br/>
Isso define o endereço da página da web que você quer acessar.

* Definir os cabeçalhos: HEADERS = {'user-agent':'Mozilla/5.0 ...}<br/>
Aqui, você está configurando um cabeçalho que imita o comportamento de um navegador web. Isso pode ajudar a garantir que o site aceite a sua solicitação como se fosse um usuário real.

* Fazer a requisição: requisicao = requests.get(URL, headers=HEADERS)<br/>
Essa linha envia uma solicitação para o site no endereço definido, usando os cabeçalhos especificados. Em resposta, você recebe o conteúdo da página.

* Tratar o conteúdo da resposta: tratamento = BeautifulSoup(requisicao.text, 'html.parser')<br/>
Depois de receber o conteúdo da página, essa linha usa a biblioteca BeautifulSoup para transformar o HTML da página em um formato mais fácil de manipular e analisar.

* O código site = tratamento.find('table', {'class':'default-table'}) faz o seguinte:<br/>

    _Dentro do HTML armazenado em 'tratamento', ele procura pela primeira tag 'table' que tenha a classe default-table.<br/>_
    _Depois de encontrar essa tabela, o resultado é armazenado na variável 'site'._<br/>

```
URL = 'https://www.infomoney.com.br/ferramentas/cambio/'
HEADERS = {'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36'}

requisicao = requests.get(URL, headers=HEADERS)
tratamento = BeautifulSoup(requisicao.text, 'html.parser')

site = tratamento.find('table', {'class':'default-table'})
```
* Nesta etapa usamos a bibliotéca Pandas para ler tabelas HTML diretamente de uma string ou de um arquivo HTML, armazená-las na variável "tabela" e excluírmos colunas irrelevantes.

```
tabela = pd.read_html(str(site))
tabela = tabela[0].drop(columns=['Unnamed: 1'])
```
___________________________________________________________________________________________________
**INTEGRAR COLAB E PLANILHAS**
* auth.authenticate_user()<br/>
Esta linha pede ao usuário para se autenticar com sua conta do Google. No Google Colab, isso geralmente abre uma janela para você fazer login e conceder permissões ao Colab para acessar seus dados do Google.

* creds, _ = default()<br/>
Aqui, a função default() é usada para obter as credenciais de autenticação padrão após o usuário ter se autenticado. Ela retorna duas variáveis: as credenciais (creds) e uma outra variável que não é usada aqui (por isso o '_').

* gc = gspread.authorize(creds)<br/>
gspread é uma biblioteca que permite interagir com o Google Sheets. A função authorize(creds) usa as credenciais obtidas para autorizar o acesso à sua conta do Google Sheets.

* gc é a variável que você usará para interagir com o Google Sheets, como ler e escrever dados em suas planilhas.
```
auth.authenticate_user()
creds, _ = default()
gc = gspread.authorize(creds)
```
* Este bloco de código cria uma planilha vazia no ambiente do Google Planilhas.
```
gc.create('Cotacao')
```
*  Este bloco de código abre a planilha mencionada entre parênteses e a armazena na variável 'planilha'.
```
planilha = gc.open('Cotacao')
```
*  Este bloco de código converte os dados da variável 'tabela' para CSV e importa o arquivo para a planilha selecionada no primeiro parâmetro exigido (no meu caso eu utilizei 'variável.id' pois é exigido o ID da planilha destino).
```
gc.import_csv(planilha.id, tabela.to_csv(index=False))
```















