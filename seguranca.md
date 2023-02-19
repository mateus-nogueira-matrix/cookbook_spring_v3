Analisando nossos projetos podemos perceber que nossa API, até este momento, não possuía nenhuma segurança, ou seja, qualquer pessoa poderia acessar todos os nossos endpoints e ter acesso à todos os recursos livremente. Precisamos entender que algumas aplicações contém informações vitais como: dados pessoais, dados bancários, usuário e senha de acesso, e portanto precisamos garantir que a nossa API e estes dados estejam devidamente protegidos. E para isto podemos contar com a dependência do Spring chamada **Spring Security** (adicionada acima).

A **Spring Security** é um framework para Java, que provê autenticação, autorização, filtros e diversas outras funcionalidades para aplicações corporativas, com o objetivo de proteger a nossa aplicação contra acessos indevidos. Iniciado em 2003 por Ben Alex, a Spring Security é distribuída sob a licença Apache Licence.

A Dependência **spring-boot-starter-security** é responsável por todas as dependências relacionadas à segurança do Spring. Dentro desta dependência, existem outras 3 dependências:

- **spring-security-core:** Implementa os principais recursos do Spring Security

- **spring-security-config:** Fornece o namespace (contexto) Spring Security

- **spring-security-web:** fornece filtros e outros recursos necessários para proteger aplicativos da web 

Vamos adicionar a **Dependência Spring Security** no Projeto Blog Pessoal, adicionando as linhas abaixo no arquivo **pom.xml**, Salvar e Executar a aplicação:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Abra a sua aplicação no Navegador da Internet através do endereço: **http://localhost:8080** e veja o que acontece.

<div align="center"><img src="https://i.imgur.com/k19Ci4j.png?2" title="source: imgur.com" /></div>

Como num passe de mágica...

- … Você tem uma página de login gerada automaticamente.
- … Você não pode mais executar solicitações GET, POST, PUT e DELETE.
- … Todo o seu aplicativo está bloqueado e solicita que você insira um nome de usuário e uma senha, que não existe.

Tendo sobrevivido ao susto inicial, você deve ter pensado: Como tudo isso aconteceu?

Depois de inserir a Dependência **spring-boot-starter-security**, o Spring habilitará alguns padrões de configuração de segurança automáticas, incluindo a segurança de todos os terminais, bem como a definição da estratégia de negociação de conteúdo no formato **httpBasic** (Autenticação básica através de usuário e senha). 

Além disso, sem fazer nenhuma configuração adicional, um nome de usuário padrão ("user'") e uma senha gerada aleatoriamente (visível no console do Spring, como mostra a imagem abaixo) serão adicionados. Essas credenciais podem ser usadas para fazer login no aplicativo (veja a imagem anterior).

<div align="center"><img src="mudar" title="source: imgur.com" width="70%"/></div>

No Spring, com a segurança habilitada por padrão, um ponto de entrada de login é usado automaticamente para redirecionar a solicitação não autenticada. O redirecionamento está em uma página de login padrão (imagem acima). No entanto, em um serviço REST, exibir uma página de login não faz sentido, pois os serviços Rest, na maioria das vezes, serão chamados por alguns aplicativos clientes personalizados, em vez de solicitações do navegador. 

Serviços REST solo (sem aplicativos da Web ou que serão criados o Front-end), é recomendado enviar a resposta HTTP Status **401 - Unauthorized**, quando a solicitação vier sem a autenticação, em vez de redirecionar para a página de login gerada por padrão para obter a autenticação. Esteja ciente de que, nesse caso, o Status **401 - Unauthorized** significa, não autenticado.

<h2>Como funciona a Spring Security?</h2>

Veja como a Spring Security funciona na figura abaixo:

<div align="center"><img src="https://i.imgur.com/KWYDoep.png" title="source: imgur.com" /></div>

1) O Usuário informa as suas credenciais (usuário e senha);
2) A requisição HTTP gerada passará pelos **Servlets Filters**;
3) Passando pelos filtros habilitados, a requisição será enviada para o **Authentication Manager**, que buscará um autenticador para a aplicação.
4) O **Authetication Provider** é o autenticador da aplicação, ou seja, ele fará a pesquisa no Banco de dados para validar o usuário e a senha, através da Interface **UserDetailsService**.
5) Após a validação, o **Authentication Manager** gera uma resposta para a requisição confirmando a autenticação (**OK 🡪 200**) ou negando o acesso ao usuário (**UNAUTHORIZED 🡪 401**).

O esquema de autenticação descrito acima é o **HTTP Basic**, onde o usuário digita o **e-mail (usuário) e a senha** e através de um endpoint não protegido, chamado **entrypoint**, o Spring Security checará se o usuário existe em nosso Banco de dados e na sequência validará a senha. Se o usuário e a senha estiver correta, o Spring Security devolverá a resposta com o HTTP Status **OK 🡪 200**. Caso contrário será devolvido o HTTP Status **UNAUTHORIZED 🡪 401**.

Para melhor compreensão deste sistema, é necessário dividi-lo em 2 ecossistemas: **Usuário** e **Segurança**, que veremos mais a frente.