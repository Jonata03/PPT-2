Documentaçao de atividade 2

AWS
**configuração do rds**

**configuraçao do efs**

**configuração dos security groups**
No total usados foram 3 securitys groups para evitar conflitos com portas e regras.

Security group para o **load balancer**: Regras de entrada e saida para a porta 80 com origem de todo ipv4
  
Security group para **instâncias ec2**: Regras de entrada e saida na porta 22 com origem de meu ip, porta 80 com origem do security group do load balancer.<br>
    
Security group para **RDS**: Regras de entrada e saida na porta 3306 com o origem de todo ipv4

**configuraçao do load balancer**

**configuração do auto scaling**

**instancia ec2**

DOCKER

container 


instalação do docker-compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
