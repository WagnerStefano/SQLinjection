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
nos mostrando que existe um erro na Sintaxe SQL que foi passada ao banco de dados para confirmação/autenticação do usuário e senha
