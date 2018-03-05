# Configurações de ambiente

#### 1. Instalação do JRuby

##### 1.1 Instalar a RVM

Primeiro instale o `mpapis public key` para gerar uma chave de segurança:

> gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

Rode os seguintes comandos no servidor para instalar o RVM:

> curl -sSL https://get.rvm.io | sudo bash -s stable

##### 1.2 Instalar o JRuby

Rode o seguinte comando para instalar a versão 1.7.11 do JRuby:

> rvm install jruby --1.7.11

Com o JRuby instalado assegure que ele será o interpretador rodando:

> rvm use jruby-1.7.11

#### 2. Instalação do Java

No diretório `webserver_v30_QAS/configurar_linux"` rode o seguinte comando para instalar:

> _sudo rpm -i jdk-8u151-linux-x64.rpm_

Após isso verifique se a instalação foi feita com sucesso rodando o comando `java -version` 

#### 3. Instalação das Gems

No diretório `webserver_v30_QAS/gems/Gems Local` rode o seguinte comando:

> _gem install --local *.gem_


# Instalação do Certificado HTTPS

#### 1. Gerar um arquivo .P12

Arquivos necessários:

- certificados.pem
- chave.key

Dentro do diretório rode o seguinte comando:

> _openssl pkcs12 -export -name bemol -in certificados.pem -inkey chave.key -out bemol.p12_

Será gerado o arquivo `bemol.p12` 

#### 2. A partir do arquivo .p12 gerar um .jks


Arquivos necessários:

- bemol.p12

Dentro do diretório rode o seguinte comando:

> _keytool -importkeystore -srckeystore bemol.p12 -srcstoretype pkcs12 -destkeystore bemol.jks -deststoretype JKS_


Será gerado o arquivo `bemol.jks` 

#### 3. Configuração do certificado no Torquebox

##### 3.1 Configuração do .jks no diretório do Torquebox

Cole o arquivo gerado `bemol.jks`  no sequinte diretório:

> _/home/bemol/.rvm/gems/jruby-1.7.11/gems/torquebox-server-3.2.0-java/jboss/standalone/configuration_

##### 3.2 Configuração do standalone.xml no diretório do Torquebox

No mesmo diretório do `bemol.jks` no arquivo `standalone.xml` cole o seguinte código:

       
    <connector name="https" protocol="HTTP/1.1" scheme="https" socket-binding="https" secure="true">
        <ssl name="ssl" key-alias="bemol" password="bemol2010" certificate-key-file="${jboss.server.config.dir}/bemol.jks" protocol="TLSv1" verify-client="false" certificate-file="${jboss.server.config.dir}/bemol.jks"/>
    </connector>

Agora o servidor já está pronto para rodar em `HTTPS`.

#### 4. Extras

##### 4.1 É possível também gerar um arquivo .jks a partir de um arquivo .pfx

No diretório onde se encontra o `arquivo.pfx` com os certificados rode o seguinte comando:

> _keytool -importkeystore -srckeystore <arquivo.pfx> -srcstoretype pkcs12 -destkeystore <bemol.jks> -deststoretype JKS_

Troque o alias do .jks gerado:

> _keytool -changealias -keystore <arquivo.jks> -alias [Nome do alias antigo]_ 

Após isso siga os passos indicados em `3. Configuração do certificado no Torquebox`


# Running WebServer

No diretório em que se encontra o `webserver_v30_QAS` rode o seguinte comando para buildar a aplicação:

> _torquebox deploy webserver_v30_QAS --env=production_

Ainda no mesmo diretório rode o seguinte comando para subir a aplicação:

> _torquebox run -p 4567 --bind-address=0.0.0.0_

Aguarde a mensagem: `Started 146 of 214 services (67 services are passive or on-demand)`, então o servidor já estará rodando em produção.

# Configuração do Netdata

O Netdata é um sistema para monitoramento de servidores em real-time.

#### Instalação

Para instalar o netdata rode o seguinte comando:

> _bash <(curl -Ss https://my-netdata.io/kickstart.sh)_

Após a instalação é necessário startar a aplicação usando o comando:

> _systemctl start netdata_

Depois de startado o netdata estará disponível em `http://<IP>:<PORT>`, exemplo: http://180.200.5.10:4567

<img src="http://prntscr.com/in6eq0"/>





