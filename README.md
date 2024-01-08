Documentaçao de atividade 2

AWS
**Criação do rds**

**Criação do efs**:<br>
vá na barra de pesquisa busque pelo serviço nfs, clique em criar sistema de arquivos e em personalizar, segue as configurações usadas:
tipo de sistema: regional<br>
backups automaticos: desabilitados<br>
ciclo de vida, mantém padrao<br>
criptografia: desabilitada<br>
modo de taxa de transferência avançado e elastic<br>
rede:selecionada a mesma vpc padrao do projeto com as duas zonas selecionadas<br>



**configuração dos security groups**<br>
No total usados foram 3 securitys groups para evitar conflitos com portas e regras.<br>

Security group para o **load balancer**: Regras de entrada e saida para a porta 80 com origem de todo ipv4.<br>
  
Security group para **instâncias ec2**: Regras de entrada e saida na porta 22 com origem de meu ip, porta 80 com origem do security group do load balancer.<br>
    
Security group para **RDS**: Regras de entrada e saida na porta 3306 com o origem de todo ipv4

**configuraçao do load balancer**

**configuração do auto scaling**

**instancia ec2**

DOCKER

container 


instalação do docker-compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose <br>
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
