# Documentação de atividade 2

## AWS

### Criação da VPC<br>  
Busque pelo serviço de VPC e selecione "Criar VPC". Seguem as configurações utilizadas:
> Recursos a serem criados: VPC e muito mais;
Geração automática da etiqueta de nome: habilitado;
Atribua um nome às etiquetas;
Bloco CIDR IPv4: 10.0.0.0/16;
Nenhum bloco CIDR IPv6;
Locação: Padrão;
Número de AZs: 2;
Número de sub-redes públicas: 2;
Número de sub-redes privadas: 2;
Gateways NAT (USD): 1 por AZ;
Endpoints da VPC: nenhum;
Habilitar nomes de host DNS: habilitado;
Habilitar resolução de DNS: habilitado;
![Captura de tela 2024-01-10 082904](https://github.com/Jonata03/PPT-2/assets/120826522/e78ca1f3-29f6-4f55-a04f-6642218d546a)


### Configuração dos security groups<br>
No total, foram usados 3 security groups para evitar conflitos com portas e regras.<br>
- Security group para o **load balancer**: Regras de entrada e saida para a porta 80 e 443 com origem de todo ipv4.
  ![Captura de tela 2024-01-10 002037](https://github.com/Jonata03/PPT-2/assets/120826522/24c8a25b-8125-480f-9090-25a83e8a761f)

- Security group para **instâncias ec2**: Regras de entrada e saida na porta 22 e 2049 com origem de qualquer IPv4, porta 80 com origem do security group do load balancer.

  ![Captura de tela 2024-01-10 001630](https://github.com/Jonata03/PPT-2/assets/120826522/1f8afde2-6b52-4702-be88-92263c4ba06f)

- Security group para **RDS**: Regras de entrada e saida na porta 3306 com o origem de todo ipv4<br>
  ![Captura de tela 2024-01-10 002642](https://github.com/Jonata03/PPT-2/assets/120826522/7cb6442f-62fc-4615-a5d4-20e04bed6f33)
---
### Criação do RDS 
Busque pelo serviço de RDS, selecione "Banco de dados" e "Criar banco de dados", segue as configurações:<br>
> Método de criação padrão;
Mecanismo do MySQL;
Mostrar versões compatíveis com o cluster de banco de dados Multi-AZ: Desabilitado;
Mostrar versões compatíveis com as gravações otimizadas do Amazon RDS: Desabilitado;
Versão: MySQL 8.0.31;
Modelo: Nível gratuito;
De um nome de identificação, usuário e senha;
Mostrar classes de instância compatíveis com gravações otimizadas do Amazon RDS: Desabilitado;
Incluir as classes de geração anteriores: Desabilitado;
Classe: db.t3.micro;
Tipo de armazenamento: SSD de uso geral (gp2);
Armazenamento: alocado 20 GB;
Não conectar a um recurso de computação do EC2;
Tipo de rede: IPv4;
VPC: mesma criada no início;
Sub-rede de banco de dados: padrão;
Acesso público: sim;
Grupo de segurança: apenas o grupo de segurança criado para o RDS;
Zona de disponibilidade: Sem preferência;
Sem proxy;
Porta do banco de dados: 3306;
Autentificação por senha;
De um nome ao banco de dados;
Grupo de parâmetros do banco de dados: Default;
Grupo de opções: Default;
Sem backup e criptografia;
Proteção contra exclusão: Habilitado;
---
Pontos de Atenção: ficar atento ao identificador do DB, Senha, VPC, Security group e ao nome do banco de dados.
### Instância ec2 
Busque pelo serviço EC2, vá em "Instâncias" e em "Executar Instâncias". Criando instância:
> Adicione as tags necessarias;<br>
Em inicio rápido selecione Amazon Linux AWS;<br>
Tipo de instância: t2.micro;<br>
Em par de chaves selecione "Criar novo par de chaves", de um nome ,tipo RSA e em formato .pem(faça o dowload e deixe na sua maquina);<br>
Nas configurações de rede clique em "Editar";<br>
Selecione a vpc criada anteriormente;<br>
Uma subnet publica na regiao desejada;<br>
Atribuir IP público automaticamente: Habilitado;<br>
Grupos de segurança: security group criado anteriormente para instâncias ec2 e RDS, selecione os 2 security group;<br>
As configuraçoes apartir do armazenamento foi mantido no padrão;<br>

Na parte inferior da última aba de configuração havera um quadrado de digitação onde se deve colocar um script para que sua intância inicie configurada, 

![Captura de tela 2024-01-10 004322](https://github.com/Jonata03/PPT-2/assets/120826522/b06e8ce3-403b-4fd9-8e01-63f551c2f119)

segue o script usado para iniciar a intância;

```
#!/bin/bash<br>
sudo yum update -y<br>
sudo yum install -y docker<br>
sudo service docker start<br>
sudo systemctl enable docker<br>
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose<br>
sudo chmod +x /usr/local/bin/docker-compose<br>
docker --version<br>
docker-compose --version
```
<br>**Crie uma imagem da instância**<br>
Ainda na aba de instâncias, selecione a instância, clique em "Ações","Imagem e modelo" e "Criar imagem";<br>
> De um nome e uma descrição a imagem;<br>
Não reinicializar: habilitado;<br>
Volume: mantido no padrão;<br>
Marcar imagem e snapshots juntos;<br>
---
### Criação do EFS
Busque pelo serviço NFS, clique em "Criar sistema de arquivos" e em "Personalizar", segue as configurações usadas:
> tipo de sistema: regional<br>
backups automaticos: desabilitados<br>
ciclo de vida, mantém padrao<br>
criptografia: desabilitada<br>
modo de taxa de transferência avançado e elastic<br>
rede:selecionada a mesma vpc padrao do projeto com as duas zonas selecionadas<br>

Conecte-se com sua intância EC2 e entre com usuario root, use o comando 
```
mkdir /efs
```
para criar um diretorio no onde sera possivel montar o serviço NFS, em seguida selecione seu EFS e clique 
em "Anexar" copie o codigo e cole no terminal da sua intância, o comando é algo semelhante a
``` 
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport URL:/ /efs
```
---
### Configuraçao do load balancer
Busque pelo serviço de ec2 e vá até "Balanceadores de carga";
<br>
Obs: para o load balancer funcionar com o Auto Scaling, foi mudado de classic para application load balancer;<br>
<br>Na aba ao lado clique em grupos de destino e em criar grupo de destino; <br>
Configurações do **grupo de destino**:<br>
> Tipo de destino: instâncias;<br>
De um nome ao grupo de destino;<br>
Protocolo: HTTP Porta: 80;<br>
Tipo de endereço IP: IPv4;<br>
VPC: mesma criada anteriormente;<br>
Versão dos protocolo: HTTP1;<br>
Protocolo da verificação de integridade: HTTP;<br>
Caminho da verificação de integridade: /;<br>
Configurações avançadas de verificação de integridade sao os padrões;<br>
Selecione as intâncias e as adicione na porta 80;<br>

Na mesma aba ao lado selecione "Load Balancer" e em "criar load balancer".<br>
> Crie um "Aplication Load Balancer";<br>
De um nome ao load balancer;<br>
Voltado a internet: Habilitado;<br>
Tipo de endereço IP IPv4;<br>
Selecione a VPC;<br>
Grupo de segurança: selecione apenas o próprio security group criado para o load balancer;<br>
AWS Global Accelerator: destivado;<br>
Listener: protocolo http, porta 80 e grupo de destino é o criado anteriormente;<br>

![Captura de tela 2024-01-10 011701](https://github.com/Jonata03/PPT-2/assets/120826522/c48e3fef-35be-4940-a8d8-1da0a62c5a52)

---

### Configuração do Auto scaling
Ainda no serviço EC2 na aba ao lado procure por Auto Scaling Group que estará na última opção, clique em "Criar grupo de Auto Scaling";<br>
De um nome ao auto scalling;<br>
clique em "Criar um modelo de execução";<br>
**configurações do modelo de execução**<br>
> De um nome e uma descriao ao modelo;<br>

Fornecer orientação para me ajudar a configurar um modelo que eu possa usar com o Auto Scaling do EC2: habilitado;<br>
Minha AMIs e selecione a imagem criada anteriormente;<br>
Tipo de instancia: t2.micro;<br>
Par de chaves: selecione a mesma par de chave ja criada;<br>
Sub-rede: escolha uma sub-rede publica na regiao desejada;<br>
Grupos de segurança: security group criado anteriormente para instâncias ec2 e RDS, selecione os 2 security group;<br>
Volume: mantem padrão;<br>
Retorne para a aba do navegador de estavamos criando o auto scaling;<br>
clique na seta circular que esta ao lado do modelo de execuçao para recarregar os modelos;<br>
Selecione o modelo recem criado e em "proximo";<br>
Em VPC escolha a vpc que estamos usando para o projeto;<br>
Zonas de disponibilidade e sub-redes: selecione todas as publicas;<br>
Anexar a um balanceador de carga existente;<br>
Escolha entre seus grupos de destino de balanceador de carga;<br>
Grupos de destino de balanceador de carga existentes: selecione o load balancing que ja havia sido criado;<br>
Serviço VPC Lattice não disponível;<br>
Ative as verificações de integridade do Elastic Load Balancing: Habilitado;<br>
Período de tolerância da verificação de integridade: 300 segundos;<br>
Habilitar coleta de métricas de grupo no CloudWatch: Desabilitado;<br>
Habilitar aquecimento de instâncias padrão: Desabilitado;<br>
Capacidade desejada: 1;<br>
Capacidade mínima desejada: 1;<br>
Capacidade máxima desejada: 5;<br>
Ajuste de escala automática: Nenhuma política;<br>
Proteção contra redução da escala de instâncias na horizontal: Desabilitado;<br>
Sem notifição;<br>
Importante:adicione as mesmas tags que foram usadas na instância ec2;<br>

---
## DOCKER
Conectando-se a sua intância via ssh ou através do console, log com seu usuário root, crie um diretório e com o comando 
```
mkdir /docker
```
Entre no diretório recém-criado com o comando:
```
cd /docker/
```
Crie um arquivo .yml com o editor de texto de sua preferência:
```
nano docker-compose.yml
```
Segue o script usado para a criação do container:
```
version: '3'

services:
  wordpress:
    image: wordpress
    restart: always
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: "rds1-ppt-2.cfeqj2zmbbx7.us-east-1.rds.amazonaws.com"
      WORDPRESS_DB_USER: "root"
      WORDPRESS_DB_PASSWORD: "luzia123"
      WORDPRESS_DB_NAME: "wordpress"
    volumes:
      - /efs/wordpress_data:/var/www/html
volumes:
  wordpress_data:
```
salve e saia, use 
```
docker-compose up
```
para subir o container, a imagem será baixada do docker hub então pode ser que demore um pouco. Para acessar pelo navegador o site WordPress vá até o load balancing usado no projeto e copie o Nome do DNS.
![Captura de tela 2024-01-10 023521](https://github.com/Jonata03/PPT-2/assets/120826522/2f964a58-ecfd-457c-8c91-d7c4779855a2)

Cole na barra de busca URL do seu navegador, você será direcionado para a página de instalação do WordPress.
![Captura de tela 2024-01-10 024926](https://github.com/Jonata03/PPT-2/assets/120826522/5542d842-6dc5-4c5d-b501-4e6cb57c59b6)

preencha as informações como desejado porém lembre das informações usadas
faça o login 

![Captura de tela 2024-01-10 025919](https://github.com/Jonata03/PPT-2/assets/120826522/637df137-c15d-49cf-aa25-2663d66a309e)

Seu Wordpress foi instalado com sucesso!


