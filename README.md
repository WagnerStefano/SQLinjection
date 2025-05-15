# SQLinjection

Aqui apresento uma técnica simples de SQl Injection ontem utilizaremos paremetros do banco de dados fazendo com que o mesmo retorne as informações do banco na pagina Web, uma vulnerabilidade frequente em sites amadores ou feitos por iniciantes que tendem a conectar o banco de dados diretamente sem qualquer tratamento, onde as requisições são passadas em String junto a informações Web podendo assim serem interceptadas e possibilitanto a inserção de comandos.

Site utilizado para teste <br>

    http://testphp.vulnweb.com/

  # Primeiro Acesso
  Ao acessar o site verifico que se trata de um site em PHP em um servidor Apache Versão 2.4.46 que possui algumas falhas que possibilitam uma execução de código remota ou Reverse Shell, além de possuir um Metasploit conhecido por esta falha.
  Utilizo a primeira ferramenta chamada *Dirb*, utilizada para escanear diretórios de uma pagina web baseada uma Word List précarregada ou possibilitando o uso da sua própria Word List que facilitaria em casos que você já conhece o site e precisaria buscar apenas por palavras chave melhor direcionadas.
  Com esse scan encontro diretórios padrões como /admin/, /pictures/, /vendor/ etc, até o diretório de interesse que seria /login.php onde podemos verificar se o site aceita comandos no input Login e Password passando parametros como '1=1 or e Password vazio,
  ![image](https://github.com/user-attachments/assets/a3d6e2d0-550e-42c8-b9d7-1e0119144e94)
o que deve retornar uma mensagem 
![image](https://github.com/user-attachments/assets/54c91df2-4f9e-4e03-a86b-22112507b859)
nos mostrando que existe um erro na Sintaxe SQL que foi passada ao banco de dados para confirmação/autenticação do usuário e senha, com essa confirmação podemos passar ao "Ataque", neste caso utilizarei Union que basicamente insere informações manualmente fazendo com que o site entenda a solicitação passada como uma requisição e retorne uma concatenação com informações do Banco De Dados, trazendo por exemplo a coluna User, pass e suas informações.
    Para isso verifico algumas paginas do site e em seus diretórios encontro algumas solicitações utilizando o parametro *CAT* e em teste atualizando os valores vemos que as informações são alteradas mudando o diretório e suas informações e com esses testes entendemos que podemos injetar comando SQL e receber suas respostas.
    *um teste valido é nesta pagina em que é passado o comando cat podeos passar qualquer parametro e descobrir se a pagina retornar um erro informando Banco de Dados, Versão e o caminho que é linkado ao Banco De Dados*
    # Definindo Range de Colunas
    Neste teste utilizamos o parametro Union Select seguido por números de colunas até que retorne um erro onde verificamos a quantidade de Colunas que foi definida neste Banco De Dados, existem algumas ferramentas que automatizam essa pesquisa mas neste caso em um ambiente controlaso sabemos que o site possui 11 colunas e posso utilizar essa informação da seguinte forma
    http://testphp.vulnweb.com/listproducts.php?cat=2%20union%20select%201,2,3,4,5,6,7,8,9,10,11
![image](https://github.com/user-attachments/assets/5a42ff5e-f573-4fda-abf1-cb31a4fd95d2)

Que retorna as informações sem erro, agora para um segundo teste podemos solicitar a versão e verificar a apresentação da informação <br>

        testphp.vulnweb.com/listproducts.php?cat=1 union select 1,version(),3,4,5,6,7,8,9,10,11

que nos traria a informação da seguinte maneira<br>
    ![image](https://github.com/user-attachments/assets/7f501c8a-6a20-4771-862d-00567a65c4f9)
    Para descobrir o nome do banco de dados utilizamos o Database(), então seguindo a mesma logica<br>
    
        testphp.vulnweb.com/listproducts.php?cat=-1 union select 1,database(),3,4,5,6,7,8,9,10,11
        

![image](https://github.com/user-attachments/assets/0ef768a6-b53b-4d4d-8f58-ba100fdf5343)







 # SQLMAP
 Com essas informações podemos utilizar um segundo metodo com o SQLMAP utilizando a ferramenta para expor os diretórios e navegar entre eles.
 
         sqlmap -u http://testphp.vulnweb.com/listproducts.php?cat=1 --tables -D acuart
Ao aplicar este comando você receberá as seguintes confirmações
    ![image](https://github.com/user-attachments/assets/4c3493ef-53f4-49ee-b1c1-6c629f218491)

Obtendo este resultado podemos passar ao proximo comando que seria o *DUMP*, que sendo direcionado para a coluna USERS mostrará suas informações

        sqlmap -u http://testphp.vulnweb.com/listproducts.php?cat=1 --dump -T users -D acuart

![image](https://github.com/user-attachments/assets/b12b2a3c-5f04-4117-b835-50e264b7abbb)
aqui vemos que foram encontradas as informações de usuários

# Dump
Seguindo a linha de testes, todas as informações de Dump também podem ser obtidas de maneira manual já que vimos onde o navegadores responde aos comandos SQL
    e podemos fazer esta tentativa da seguinte maneira, buscando pelo Information Schema que são sessões padrões dos bancos SQL criadas automaticamente e que podem servir de trampolim para que possamos nos aprofundar em mais informações.
    utilizando o caminho 
    
            testphp.vulnweb.com/listproducts.php?cat=-1 union select 1,schema_name,3,4,5,6,7,8,9,10,11 from information_schema.schemata
