Documentaçao de atividade 2

AWS
**Criação da VPC**<br>

**configuração dos security groups**<br>
No total usados foram 3 securitys groups para evitar conflitos com portas e regras.<br>

Security group para o **load balancer**: Regras de entrada e saida para a porta 80 com origem de todo ipv4.<br>
  
Security group para **instâncias ec2**: Regras de entrada e saida na porta 22 com origem de meu ip, porta 80 com origem do security group do load balancer.<br>
    
Security group para **RDS**: Regras de entrada e saida na porta 3306 com o origem de todo ipv4<br>

**Criação do rds**<br>

**Criação do efs**:<br>
vá na barra de pesquisa busque pelo serviço nfs, clique em criar sistema de arquivos e em personalizar, segue as configurações usadas:
tipo de sistema: regional<br>
backups automaticos: desabilitados<br>
ciclo de vida, mantém padrao<br>
criptografia: desabilitada<br>
modo de taxa de transferência avançado e elastic<br>
rede:selecionada a mesma vpc padrao do projeto com as duas zonas selecionadas<br>

**configuraçao do load balancer**<br>
busque pelo serviço de ec2 e va até balanceadores de carga;<br>
obs: para o load balancer funcionar com o Auto Scaling, foi mudado de classic para application load balancer;<br>
Na aba ao lado clique em grupos de destino e em criar grupo de destino; <br>
configurações do **grupo de destino**:<br>
tipo de destino: instâncias;<br>
de um nome ao grupo de destino;<br>
Protocolo: HTTP Porta: 80;<br>
Tipo de endereço IP: IPv4;<br>
VPC: mesma criada anteriormente;<br>
Versão dos protocolo: HTTP1;<br>
Protocolo da verificação de integridade: HTTP;<br>
Caminho da verificação de integridade: /;<br>
Configurações avançadas de verificação de integridade sao os padrões;<br>
Selecione as intâncias e as adicione na porta 80;<br>

<br>Na mesma aba ao lado selecione "Load Balancer" e em "criar load balancer".<br>
Configurações: <br>
Crie um application Load Balancer;<br>
De um nome ao load balancer;<br>
Voltado a internet;<br>
Tipo de endereço IP IPv4;<br>
Selecione a VPC;<br>
Grupo de segurança: selecione apenas o próprio security group criado para o load balancer;<br>
Listener: protocolo http porta 80 grupo de destino criado anteriormente;<br>
AWS Global Accelerator: destivado;<br>

**configuração do auto scaling**<br>

**instancia ec2**<br>

DOCKER

container 


instalação do docker-compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose <br>
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
