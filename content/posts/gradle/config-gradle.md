
---
title: "Configurando e gerenciando versões do Gradle no seu ambiente de desenvolvimento"
date: 2022-11-20T09:03:20-08:00
draft: false
---

Desenvolvedores Java que trabalham com o ecossistema Android,
geralmente, passam por dificuldades na configuração do seu ambiente de
desenvolvimento, muitos não o configuram apropriadamente e esperam que
o Google traga um toolkit junto com a IDE em um pacote autocontido
completo e funcional. Porém, você como um bom desenvolvedor que
é, não deve esperar por isso. Este guia é indicado para usuários que trabalhem em ambiente Unix ou Unix like.


Existem casos no seu dia a dia que você precisará fazer uso da interface de
linha de comando do Gradle. Um dos possíveis casos é de fazer o seu projeto
compilar em um ambiente de CI (Continuous Integration) ou criar um script que
realizará build e distribuição do apk para uma determinada lista de usuários
utilizando algum plugin de terceiros. Realizar atividades como estas podem ser
mais simples e mais facilmente comunicáveis através da linha de comando.


Inicialmente, para utilizar o Gradle pela interface de linha de comando existem
duas formas. A primeira delas consiste em instalar e configurar o Gradle
globalmente na máquina. A segunda consiste em executar diretamente o Gradle
através do Gradle Wrapper. Usualmente muitos desenvolvedores Android optam pela
segunda forma que se faz através do comando ./gradlew na pasta raiz do projeto.
Essa abordagem, apesar de ser a recomendada pelo time do Gradle, possui alguns
problemas. 

Ao utilizar o comando gradlew a ferramenta realizará o download da versão do
Gradle fixada no projeto. Caso o desenvolvedor compile outro projeto que utilize
uma versão diferente da que foi baixada anteriormente, a ferramenta realizará o
download do Gradle novamente na nova versão requerida. O que resulta em uma
duplicação desnecessária na sua máquina. Estar fixado a uma versão específica do
Gradle para o projeto tem suas vantagens. Contudo, vejo como um aspecto negativo
a perda de improvements e novas features implementadas pelo time do Gradle a
cada nova versão (pelo histórico não são poucas e muitas atreladas ao desempenho
do build). 

Outro detalhe com o qual o desenvolvedor deve ficar atento é no uso de projeto
feito por terceiros. É necessário que se tenha confiança na fonte desse arquivo
por se tratar de um binário, além de averiguar se o mesmo foi recebido através
de um canal seguro a fim de evitar possíveis problemas de segurança. Nesse
sentido, esse artigo traz como alternativa a instalação, configuração e
gerenciamento do Gradle pela interface de linha de comando auxiliado pela
ferramenta sdkman como alternativa ao modo usual de utilização do Gradle através
da linha de comando. 

Nesse contexto, é necessário que a JDK esteja devidamente configurada na máquina
em questão. Para tal, existem diversos tutoriais de como configurar a JDK para a
maioria dos sistemas operacionais, o principal deles que podemos tomar como
referência se encontra no link abaixo: 

https://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html

Após a JDK instalada e configurada, em conjunto com as devidas variáveis de
ambiente, é recomendável realizar a instalação da ferramenta sdkman que, no meu
entendimento, é extremamente interessante para desenvolvedores que não ficam só
no Java e gostam de experimentar as várias tecnologias que a JVM dispõe. O
sdkman é uma ferramenta para gerenciar versões de sdk's para as tecnologias da
JVM, na maioria dos sistemas, baseados em Unix. Para instalá-la acesse o link e
siga as instruções: 

http://sdkman.io/install.html

Essa ferramenta surgiu de um antigo projeto chamado GVM (Groovy Environment
Manager), similar ao RVM que já existia na comunidade Ruby, que tomou um
direcionamento mais generalista e se tornou o sdkman. Após a instalação da
ferramenta, é só executar o comando no terminal: 

```
sdk install gradle
```

A ferramenta então, realizará o download da versão mais atual do Gradle e
perguntará se você deseja deixá-la como padrão. A ferramenta automaticamente
realizará a configuração das variáveis de ambiente e referenciará a versão
selecionada como padrão após a confirmação do usuário. Para checar isso, basta
executar o comando: 

```
gradle -v
```

Se deu tudo certo aparecerá algo como a imagem abaixo:

![Gradle command result](/images/tuto_gradle_version.png)

Agora com o Gradle configurado globalmente será possível executar qualquer
comando Gradle através da linha de comando, no contexto dos vários projetos que
você como desenvolvedor Android estiver trabalhando. 

Após a instalação do Gradle é possível realizar uma única configuração global
para melhorar o desempenho do Gradle para todos os projetos na sua máquina. O
Gradle utiliza um arquivo chamado gradle.properties que se encontra na raiz de
cada projeto. Para utilizar globalmente o seu arquivo gradle.properties basta
criar o mesmo arquivo dentro da pasta .gradle, que fica no diretório padrão do
usuário, utilizando o seguinte o comando: 

```
touch ~/.gradle/gradle.properties
```


Existem algumas opções recomendas pelo guia de performance na documentação do
Gradle que você pode acessar através do link
https://gradle.github.io/performance-guide/. Separei algumas flags que sempre
utilizo no meu ambiente de desenvolvimento. Caso queira utilizar as mesmas
opções no seu ambiente, execute os comandos abaixo e em qualquer build do Gradle
que seja realizado na sua máquina estas flags serão habilitadas: 

```

# Para a versão 3.0 essa variável já vem habilitada
echo 'org.gradle.daemon=true' >> ~/.gradle/gradle.properties

# Por ser experimental use com moderação ;)
echo 'org.gradle.parallel=true' >> ~/.gradle/gradle.properties

echo 'org.gradle.jvmargs=-Xmx2048M' >> ~/.gradle/gradle.properties

```


Como em toda atualização liberada pelo time de desenvolvimento do Gradle, há
melhorias no desempenho. Por isso, é importante, que você tente sempre utilizar
a versão mais atual. Através do sdkman esse processo se torna extremamente
trivial e transparente. Com o comando sdk install gradle a ferramenta baixa a
versão mais recente disponível e pergunta se você deseja configurá-la como
padrão. É possível também, voltar para qualquer versão anterior e configurá-la
como padrão seguindo o comando abaixo: 


```
sdk default gradle 2.14.1
```

É possível remover alguma versão anteriormente instalada e que não está mais
sendo utilizada. É possível fazer isso com o comando: 

```
sdk uninstall gradle 2.12
```


Além disso, como bônus você ganha a possibilidade de instalar algumas
ferramentas (ant, maven e etc), frameworks (spring-boot, grails e etc) e
toolchains de compilação das linguagens alternativas na JVM como Groovy, Scala,
Ceylon e Kotlin. É possível obter uma lista de artefatos que podem ser
instalados através da ferramenta seguindo o comando abaixo: 

```
sdk list
```

O time do sdkman almeja, futuramente, incorporar o gerenciamento de versões da
JDK da mesma maneira como pode ser visto nessa issue aberta
https://github.com/sdkman/sdkman-cli/issues/338. O que torna a instalação da JDK
e configuração das variáveis de ambiente um processo muito mais simples do que é
hoje. Enfim, é um projeto interessante e uma alternativa para melhorar a
eficiência no uso das ferramentas dentro do ecossistema da JVM. 

Espero ter contribuído para tornar o dia a dia de trabalho de vocês mais prático
e divertido. Sintam-se confortáveis para enviar suas impressões sobre o sdkman,
bem como compartilhar alguma outra dica de configuração do Gradle para tornar o
build mais rápido, ou mesmo enviar críticas e sugestões nos comentários ou por
email. 
