## Jenkins step by step for testers in windows

# Bora começar!

## 🛠️ Como Instalar/Configurar o Jenkins no windows?

**Step 1.01:** No Windows o Jenkins pode ser instalado com o famoso “next next finish”, mas ficamos neste step by step com a execução do arquivo war diretamento no ruindows.

Bastas acessar o site oficial do Jenkins [Click aqui!](https://www.jenkins.io/) e buscar pela versão desejada ou suportada pelo sua maquina.

![alt text](https://i.imgur.com/PaBXq3p.gif)

O que é o arquivo war? R: É uma versão Jenkins que pode ser instalada em qualquer sistema operacional ou plataforma que execute uma versão do Java suportada pelo Jenkins.

**Step 1.02:** Arquivo "jenkins.war" baixado, basta abrir o terminal vulgo shell e abrir a pasta onde o arquivo foi baixado.

![alt text](https://i.imgur.com/iHQfgTv.gif)

**Step 1.03:** Na pasta do arquivo baixado executar o comando java -jar jenkins.war

![alt text](https://i.imgur.com/EyYdPlq.gif)

Após esses passos anteriores a mágica do "next next" se inicia. No meio do processe será gerado um token para seguir com a instalação.

Exemplo da chave durante a instalação gerada: ![alt text](https://i.imgur.com/n9KS6It.png)

**Step 1.04:** Abrir o endereço/url padrão gerada para o jenkins no navegador, geralmente quase sempre será "http://127.0.0.1:8080/"

**Step 1.05:** Neste passo basta copiar a cheve gerada no campo esperado e dar sequencia na instalação.

![alt text](https://i.imgur.com/ilaqL2e.png)

**Step 1.06:** Escolha os plugins necessario para utilização durante os tests (CD)

![alt text](https://i.imgur.com/euW1BWw.png)

**Step 1.07:** Após instalação basta criar usuario e senha realizar o acesso ao jenkins

![alt text](https://i.imgur.com/fTcupC1.png)

# Segue o fluxo...!

## ⚙️ Como executar o Jenkins war no windows ?

**Step 2.01:** Após ter realizado os passos anteriores “caso tenha fechado tudo” e deseje retornar o processo basta acessar o shell executar o comando do passo 1.03

![alt text](https://i.imgur.com/8sOaTkC.gif)

### Referencia oficial: [ref](https://www.jenkins.io/doc/book/installing/war-file/)

## 🚀 Bora colocar a mão na massa ?

**Step 3.01:** No ambiente logado bora criar um novo Job para configurar uma pipeline para execução dos test

![alt text](https://i.imgur.com/uACCvc2.gif)

**Step 3.02:** Marcações e parametrização para limpar os builds antigos evitando um acumulo de informação/arquivos no ambiente local

![alt text](https://i.imgur.com/lAIeEWp.png)

**Step 3.03:** No campo Build Triggers é definido o Schedule para agendamento de execução automática da pipeline

![alt text](https://i.imgur.com/KByFWlU.png)

**Step 3.04:** O Pipeline pode ser configurada no seguinte campo

![alt text](https://i.imgur.com/hzIJPxI.png)

E a estrutura geral fica como apresentado nesta figura abaixo:

![alt text](https://i.imgur.com/kJXQw3T.png)

**Step 3.04.1:** Descrição dos campos, 

Para execução de complilação do projeto os stages podem ser organizados da seguinte forma para execução local dos testes
Ver arquivo de configuração em repositório: pipeline

* Stage responsável por limpar o ambiente anterior:

```
		stage('CleaanUp Stage'){
            steps{
                //define the single or multiple step
                bat 'echo CleaanUp Stage'
                cleanWs()
            }
        }
```

* Stage responsável por realizar o clone do projeto no repositorio Github:

```
		stage('Git Checkout'){
            steps{
                 //define the single or multiple step
                bat 'echo Git Checkout'
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/williammelquiades/RestSharpNetCoreDesafioB2.git']]])
            }
		}
```

* Stage responsável por restaurar os pacotes utilizados no projeto:

```
        stage('Restore Package Stage'){
            steps{
                 //define the single or multiple step
                bat 'echo Restore Package'
                bat '%nuget% restore RestSharpNetCoreDesafioB2.sln'
            }
        }
```

obs.: É utilizado o arquivo .exe da próprio sistema que é cadastrado como variável de ambiente após o agente para execução.

```
agent any
    environment {
        nuget = "C:\\Data\\jenkinsWar\\nuget.exe"
    }
```

* Stage responsavel realizar o BUILD no projeto:
```
stage('Build Stage'){
            steps{
                 //define the single or multiple step
                bat 'echo Build Stage'
                bat "\"${tool 'Visual Studio 2019'}\" -verbosity:detailed RestSharpNetCoreDesafioB2.sln /p:Configuration=Release /p:Platform=\"Any CPU\""
            }
        }
```

* Stage responsável por executar os test do pacote 

```
stage('Test Execution Stage'){
            steps{
                 //define the single or multiple step
                bat 'echo Test Exeecution Started'
                bat "\"${tool 'VSTest'}\" C:/Users/Dell/.jenkins/workspace/RestSharpAutomation/RestSharpNetCoreDesafioB2/bin/Release/netcoreapp3.1/RestSharpNetCoreDesafioB2.dll /InIsolation /Logger:html"
                bat 'echo Test Exeecution Complete'
            }
        }
 ```
**Step 3.05:** Ao realizar a construção o processo gerados de forma gráfica (bonitinhas e organizada) como apresentado abaixo:

![alt text](https://i.imgur.com/un3ea2y.png)

# fluxo final...!

## 📦 Resultado final experado !

O retorno por ser verificado no log gerado pelo gráfico gerado clicado em cada um dos logs gerados nos passos ou verificando a saída do console da execução.

![alt text](https://i.imgur.com/Uln6pEP.gif)

---
⌨️ com ❤️ por [William Melquiades](https://github.com/williammelquiades)