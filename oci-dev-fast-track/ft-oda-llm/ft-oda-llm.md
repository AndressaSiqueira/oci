# Lab Extra - Usando LLM no ODA (Oracle Digital Assistant)

## Introdução

Esse workshop foi desenvolvido com o intuito de demonstrar as funcionalidades complementares e de praticidade para sua ferramenta E-Business Suite da Oracle Cloud Infrastructure 

*Tempo estimado para o Lab:* 20 Minutos

### **Pré requisitos**

Acessar o link maritaca AI
- Abra o link https://chat.maritaca.ai/auth
- Clique no botão verde Entrar e crie uma conta ou faça login como mostra o site
- Ao acessar, clique em meu plano e em "Gerencie forma de pagamento", adicione o metodo de pagamento para utilizar a API
- Após esta etapa, clique em "Chaves de API no lado inferior como mostra na imagem e gere uma chave API
- Salve sua chave para utilizar no Lab de Oracle Digital Assistant

Clique no botão verde Entrar e crie uma conta ou faça login como mostra o site

Ao acessar, clique em meu plano e em "Gerencie forma de pagamento", adicione o metodo de pagamento para utilizar a API

 Após esta etapa, clique em "Chaves de API no lado inferior como mostra na imagem e gere uma chave API
 
Salve sua chave para utilizar no Lab de Oracle Digital Assistant
Documentacões adicionais : API-MARITALK (https://github.com/maritaca)


### **Objetivos**
Nesse Workshop você vai:

- Conhecer o Oracle E-Business Suite (EBS), uma suíte integrada de aplicativos empresariais que automatiza processos de negócios e fornece uma solução completa para gerenciar diversas funções empresariais, como finanças, recursos humanos, cadeia de suprimentos e manufatura.

- O Oracle E-Business Suite Cloud Manager foi projetado para simplificar as diversas tarefas que os administradores de banco de dados (DBAs) do Oracle E-Business Suite executam diariamente, com o objetivo de reduzir o esforço necessário para realizá-las.

## Task 1: Preparar a Tenancy para o Oracle E-Business Suite

1. Efetuar login na Console da Oracle Cloud Infrastructure. Use as credenciais de administrador da tenancy para efetuar login na console do Oracle Cloud Infrastructure.

2. Registrar o Cloud Manager como uma aplicação confidencial na tenancy usando IAM com Identity Domains.
Abra o menu de navegação e clique em Identity & Security. Em Identity, clique em Domain.
![menu domain](images/menu-domain.png)

3. Selecione o root Compartment na lista suspensa Compartment. Na lista de Domains, clique no link do “Default” Domain.
![compartimento domain](images/domain-root.png)

4. Clique em Integrated Applications no menu à esquerda.

5. Clique em Add application.
![add aplication](images/add-aplication.png)

6. Selecione Confidential Application.
7. Clique em Launch Workflow. Isso leva você à página Add Confidential Application:
![confidential app](images/confidential.png)

8. Em Add application details, insira o seguinte:
-	Name: Oracle E-Business Suite Cloud Manager
-	Description: Insira uma descrição.
Clique em Next.

9. Em Configure OAuth:
Clique em Configure this application as a client now. Em Allowed Grant Types, selecione as seguintes opções:
-	Client Credentials
-	Refresh Token
-	Authorization Code

Agora, vamos definir nosso URL do Cloud Manager. Para este laboratório, use o seguinte URL de exemplo: https://myebscm.ebshol.org:443

10. Salve o URL do Cloud Manager em um arquivo key-data.txt como Cloud_Manager_URL para consulta posterior.

11. Usando o URL do Cloud Manager que você acabou de salvar, anexe a esse URL os seguintes valores, conforme mostrado, para inserir seu Redirect URL.

12. Redirect URL: <URL do Cloud Manager>/cm/auth/callback
Por exemplo: https://myebscm.ebshol.org:443/cm/auth/callback

13. Post-Logout Redirect URL: <URL do Load Balancer do Cloud Manager>/cm/ui/index.html?root=login
Por exemplo: https://myebscm.ebshol.org:443/cm/ui/index.html?root=login

14. Logout URL: deixe este campo vazio.
![confidential app](images/config-confidential.png)

15. Em Client Type, certifique-se de que o botão de opção Confidential esteja selecionado.

16. Selecione a opção Introspect para Allowed Operations.

![config auth](images/config.auth.png)

17. Em Token Issuance Policy, marque a caixa de seleção Add app roles. Clique em Add roles.
![confidential app](images/add-role.png)

18. Selecione Authenticator Client e Me. Em seguida, clique em Add.

19. Clique em Next.
![options role](images/options-role.png)

20. Em Configure policy, clique em Finish.
![finish](images/finish.png)

21. Anote os seguintes valores em seu key-data.txt (Client_ID e Client_Secret, respectivamente):

- ID do Cliente
- Client Secret (para visualizar, clique em Show secret.)

![general info](images/general-information.png)

22. Clique em Activate e confirme para ativar a aplicação confidencial.

![activate application](images/activate-application.png)

Registre o valor do Oracle Identity Cloud Service Client Tenant como Client_Tenant no key-data.txt. Isso é encontrado no Overview do Default Domain na seção Domain Information. É visto como parte da URL encontrada na URL do domínio, após "//" e antes de ".identity.oraclecloud.com". Começa com os caracteres "idcs-", seguidos por uma sequência de números e letras no formato idcs-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Por exemplo: idcs-6572bfeb183b4becad9e649bfa14a488.

![overview domain](images/overview-domain.png)

## Task 2: Implantação e configuração do Oracle E-Business Suite Cloud Manager

1. No menu de navegação da console do OCI clique em Marketplace e selecione All Aplications. 
![marketplace](images/marketplace.png)

2. Na página de aplicações do Marketplace:
•	Navegue até Filter e em seguida selecione Stack.
•	Na barra de pesquisa, digite "E-Business Suite".
•	Clique na aplicação Oracle E-Business Suite Cloud Manager Stack for Demos.

![compartment](images/compartment.png)
3. Certifique-se de que a versão padrão esteja selecionada.

4. Na lista Compartment, selecione o compartimento pai do compartimento onde a instância do Oracle E-Business Suite Cloud Manager Compute será implementada. Por exemplo, minhaempresatenancy(root).

5. Revise e aceite os Termos de Uso.

6. Clique em Launch Stack.

![licensing](images/licensing.png)

7. Na tela Stack Information insira os seguintes valores:
- Name: O padrão é Oracle E-Business Suite Cloud Manager Stack for Demos-<date&time>.
- Description: Adicione uma descrição para a stack.

As duas últimas variáveis devem conter:
- Compartment: É o compartimento selecionado previamente
- Terraform Version: selecione 0.12.x na lista. 

8. Clique em Next.
![stack](images/stack.png)

9. Na tela Configure Variables insira os seguintes valores:

- Resource Prefix: ebshol
- Single Compartment Setup: Certifique-se de que essa opção esteja selecionada.
- Parent Compartment: Selecione seu compartimento como compartimento pai para seus recursos.
- EBS Cloud Manager Administrator Username: ebscm.admin@example.com
- EBS Cloud Manager Administrator Email: Este pode ser uma conta de e-mail pessoal.
- Criar New REST API KEY: certifique-se de que esta caixa de seleção esteja marcada.

![create stack](images/create-stack.png)

Server Host for EBS Cloud Manager Login URL: myebscm.ebshol.org
- EBS Cloud Manager Shape: VM.Standard.E2.2
- EBS Cloud Manager Admin Password:
- Essa senha é o que permitirá que você faça login na instância do Cloud Manager e PODE ser diferente da senha do usuário para efetuar login na Console do OCI.

Adicione esta senha ao seu arquivo key-data.txt.
- Public Key: Use uma chave ssh existente ou gere uma nova. Cole a chave pública.
Para obter mais informações sobre chaves ssh, visite: Gerando um par de chaves SSH (https://www.oracle.com/webfolder/technetwork/tutorials/obe/cloud/compute-iaas/generating_ssh_key/generate_ssh_key.html)
- EBS Cloud Manager Availability Domain: Escolha o domínio de disponibilidade que termina em -1 na lista. 
- Para regiões que só possuem um domínio de disponibilidade não é necessário escolher.

![cloud manager details](images/cloud-manager-details.png)
- Custom CIDR Ranges: Deixe esta caixa de seleção desmarcada.
- EBS Cloud Manager Access CIDR: 0.0.0.0/0

Insira os valores do arquivo key-data.txt da seguinte forma:
1-	IDCS Client ID:  Client_ID
2-	IDCS Client Secret: Client_Secret
3-	IDCS Client Tenant: Client_Tenant
Click Next.

![cloud manager network](images/cloud-manager-network.png)

10. Na tela Review Information, verifique as informações e clique em Create.

![review](images/review.png)

11. Isso leva você à página Stack Details da stack recém-criada. Você notará que o status do trabalho passará por Accepted, In progress e Succeeded. Isso levará algum tempo para ser concluído.

![resource manager](images/resource-manager.png)

12. Depois que o job for bem-sucedido, você terá todos os recursos de rede (VCN, Load Balancer, subnets e assim por diante) necessários para implantar a instância do Oracle E-Business Suite Cloud Manager.

13. Você encontrará os detalhes relacionados à instância do EBS Cloud Manager e ao Load Balancer na parte inferior da saída de log (Outputs), conforme mostrado na captura de tela a seguir:

![instance](images/instance.png)

14. Copie e cole IP privado, IP público, URL de login e IP público do Load Balancer em seu arquivo key-data.txt. Essas variáveis são necessárias para o restante dos procedimentos deste laboratório.


## Task 3: Logar no Oracle E-Business Suite Cloud Manager.

1. Antes de fazer login na página web do EBS Cloud Manager, você precisa adicionar hostname ao arquivo de hosts do seu computador local. Siga as instruções abaixo para realizar esta configuração:
- Edite o arquivo Local Hosts em seu computador e adicione uma entrada.
Para Windows:
- Navegue até o Bloco de Notas no menu Iniciar.
- Passe o mouse sobre o Bloco de notas, clique com o botão direito e selecione a opção Executar como administrador.
- No Bloco de Notas, navegue até Arquivo > Abrir.
- Navegue até C:\\Windows\System32\drivers\etc.
- Encontre o arquivo "hosts".
- No arquivo hosts, role para baixo até o final do conteúdo.
- Adicione a seguinte entrada bem no final do arquivo: <lb_public_ip> myebscm.ebshol.org
- Salve o arquivo.

![windows](images/windows.png)

2. Digite o URL de login gerado e documentado em seu key-data.txt anteriormente no seu navegador.

3. Faça login no Oracle E-Business Suite Cloud Manager usando suas credenciais IDCS para a conta do EBS Cloud Manager, conforme documentado em seu arquivo key-data.txt.

Observação: pode levar algum tempo adicional para que o balanceador de carga seja configurado corretamente. Às vezes, a configuração pode levar até 30 minutos ou uma hora. Se você quiser verificar o status do balanceador de carga, na console do OCI vá para Networking > Load Balancers e verifique o status geral do Heatlh. Se estiver tudo bem, a conexão pode ser estabelecida.
![log in](images/log-in.png)

3. Uma vez logado, você estará na Environments page.

![envolviment](images/envolviment.png)

## Conclusão

### Parabéns!!!👏🏻 Laboratório concluído. Você pode continuar executando outros laboratórios relacionados ao tema na página : Lift and Shift On-Premises EBS to OCI (https://apexapps.oracle.com/pls/apex/r/dbpm/livelabs/run-workshop?p210_wid=672&p210_wec=&session=14855038387887).

## Autoria

- **Autores** - Andressa Siqueira
- **Último Update Por/Date** - Andressa Siqueira Maio/2024
