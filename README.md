*This project has been created as part of the 42 curriculum by fsayuri-.*

# Born2beRoot

## Descrição

O Born2beRoot é um projeto introdutório de administração de sistemas e virtualização da 42. O objetivo é configurar um servidor Linux dentro de uma máquina virtual, aplicando conceitos básicos de segurança, gerenciamento de usuários, armazenamento, serviços e automação.

A máquina foi configurada sem ambiente gráfico, utilizando LVM com criptografia para o armazenamento. Também foram configurados o AppArmor, o firewall UFW, o serviço SSH na porta `4242`, políticas de senha, regras específicas para o uso do `sudo` e grupos de usuários.

O acesso remoto por SSH foi configurado para não permitir login direto como `root`. O firewall foi configurado para permitir somente a porta necessária para o SSH. Também foi criado um script de monitoramento em Bash, executado periodicamente pelo `cron`, que coleta informações do sistema.

## Sistema Operacional

O sistema operacional escolhido para este projeto foi o **Debian**.

O Debian é uma distribuição Linux conhecida pela estabilidade, pela ampla documentação e pela grande quantidade de pacotes disponíveis. Ele utiliza o APT para gerenciamento de pacotes. Neste projeto, o sistema também foi configurado utilizando AppArmor e UFW.

Além disso, o próprio subject recomenda o Debian para quem está começando em administração de sistemas e informa que a configuração do Rocky Linux é mais complexa.

### Vantagens e desvantagens

Entre as principais vantagens do Debian estão a estabilidade, a ampla documentação e uma configuração relativamente simples para um primeiro contato com administração de sistemas.

Como desvantagem, por priorizar estabilidade, o Debian pode utilizar versões mais conservadoras de alguns pacotes, que nem sempre correspondem às versões mais recentes disponíveis.

## Principais Decisões de Projeto

As principais decisões de configuração foram baseadas nos requisitos obrigatórios do subject, priorizando uma instalação simples, funcional e com o menor uso de disco possível sem comprometer o funcionamento da máquina virtual.

### Particionamento

Os tamanhos das partições foram escolhidos com o objetivo de reduzir o tamanho final do disco virtual e, consequentemente, tornar operações como clonagem e cálculo da assinatura SHA-1 mais rápidas.

Ao mesmo tempo, foi mantido espaço suficiente para que o sistema operacional e todos os requisitos obrigatórios do projeto funcionassem corretamente.

O disco virtual foi criado com aproximadamente **8 GB**, utilizando alocação dinâmica de espaço.

A estrutura de particionamento atual é aproximadamente:

```text
sda                         8G
├─sda1                    838M   /boot
├─sda2                      1K
└─sda5                    7.2G
  └─sda5_crypt            7.2G
    ├─fsayuri--42--vg-root
    │                     6.4G   /
    └─fsayuri--42--vg-swap_1
                          780M   [SWAP]
```

A partição `sda5` foi criptografada e utilizada como base para o LVM (**Logical Volume Manager**).

Dentro do Volume Group `fsayuri-42-vg`, foram criados dois Logical Volumes:

* `root`, com aproximadamente **6,4 GB**, utilizado pelo sistema de arquivos principal e montado em `/`;

* `swap_1`, com aproximadamente **780 MB**, utilizado como área de swap.

Essa estrutura mantém o sistema simples e com baixo uso de armazenamento, atendendo ao requisito obrigatório de utilizar pelo menos dois volumes lógicos dentro da área criptografada, sem utilizar o esquema adicional de particionamento apresentado na parte bônus.

### Segurança

As configurações de segurança foram implementadas de acordo com os requisitos do projeto. Entre elas estão:

- política de senhas fortes;
- restrições para o uso do `sudo`;
- registro das ações executadas com `sudo`;
- AppArmor ativo;
- firewall UFW ativo;
- acesso SSH pela porta `4242`;
- bloqueio de login SSH diretamente como `root`.

### Gerenciamento de usuários

Além do usuário `root`, foi criado um usuário com o login da 42.

Esse usuário pertence aos grupos `sudo` e `user42`, permitindo o uso controlado de privilégios administrativos.

### Serviços

Como o objetivo era manter um servidor mínimo, foram instalados e configurados apenas os serviços necessários para a parte obrigatória do projeto.

Os principais serviços e ferramentas utilizados foram:

- SSH, para acesso remoto;
- UFW, para gerenciamento das regras de firewall;
- AppArmor, como camada adicional de segurança;
- `cron`, para execução automática do script de monitoramento.

## Comparações

### Debian vs Rocky Linux

Debian e Rocky Linux são distribuições Linux, mas pertencem a ecossistemas diferentes e utilizam algumas ferramentas distintas para administração do sistema.

O Debian prioriza estabilidade e possui uma grande comunidade e ampla documentação. Utiliza o APT para gerenciamento de pacotes e, no contexto deste projeto, utiliza AppArmor e UFW.

O Rocky Linux faz parte do ecossistema compatível com o Red Hat Enterprise Linux (RHEL). Utiliza DNF para gerenciamento de pacotes e, no Born2beRoot, utiliza SELinux e firewalld.

| Debian | Rocky Linux |
| --- | --- |
| Ecossistema Debian | Ecossistema RHEL |
| APT | DNF |
| AppArmor no projeto | SELinux no projeto |
| UFW no projeto | firewalld no projeto |
| Recomendado pelo subject para iniciantes | Configuração considerada mais complexa pelo subject |

Escolhi o Debian por ser recomendado pelo próprio subject para quem está iniciando em administração de sistemas e por sua estabilidade e ampla documentação.

### AppArmor vs SELinux

AppArmor e SELinux são mecanismos de segurança que adicionam uma camada de controle sobre o que processos e aplicações podem acessar ou executar no sistema.

O AppArmor utiliza perfis para definir as permissões dos programas e trabalha principalmente com regras baseadas em caminhos de arquivos.

O SELinux utiliza políticas de segurança e atribui contextos ou labels aos recursos e processos do sistema, permitindo um controle mais granular sobre suas interações.

| AppArmor | SELinux |
| --- | --- |
| Utilizado no Debian deste projeto | Utilizado no Rocky neste projeto |
| Baseado principalmente em caminhos | Baseado em labels e contextos |
| Perfis por aplicação | Políticas de segurança |
| Geralmente mais simples de configurar | Geralmente oferece controle mais granular |

Neste projeto foi utilizado o **AppArmor**, pois o sistema escolhido foi o Debian.

### UFW vs firewalld

UFW e firewalld são ferramentas utilizadas para facilitar o gerenciamento das regras de firewall do sistema.

O **UFW (Uncomplicated Firewall)** possui uma interface simples para criação e remoção de regras. No projeto, ele foi utilizado para permitir a conexão SSH pela porta `4242` e bloquear acessos que não foram explicitamente permitidos.

O **firewalld** é utilizado no Rocky Linux para a mesma finalidade geral, mas possui uma abordagem baseada em **zonas**, permitindo aplicar diferentes conjuntos de regras dependendo da interface ou do nível de confiança da rede.

| UFW | firewalld |
| --- | --- |
| Utilizado no Debian deste projeto | Utilizado no Rocky neste projeto |
| Configuração simples por regras | Organização baseada em zonas |
| Interface direta para gerenciamento das regras | Permite diferentes conjuntos de regras por zona |

Neste projeto foi utilizado o **UFW**, mantendo apenas a porta necessária para o SSH aberta na parte obrigatória.

### VirtualBox vs UTM

VirtualBox e UTM permitem criar e executar máquinas virtuais, possibilitando a execução de um sistema operacional convidado isolado do sistema operacional da máquina física.

O **VirtualBox** é uma solução de virtualização amplamente utilizada e foi a ferramenta utilizada neste projeto.

O **UTM** é uma alternativa permitida pelo subject quando não é possível utilizar o VirtualBox, sendo especialmente comum em computadores Mac com processadores Apple Silicon. Ele utiliza tecnologias como QEMU para virtualização e emulação.

| VirtualBox | UTM |
| --- | --- |
| Utilizado neste projeto | Alternativa permitida pelo subject |
| Muito utilizado para virtualização em computadores x86 | Muito utilizado em Macs, inclusive Apple Silicon |
| Utiliza discos como `.vdi` | Pode utilizar discos como `.qcow2` |

Escolhi o VirtualBox porque estava disponível e atendia diretamente aos requisitos do projeto.

## Instruções

O projeto é executado através de uma máquina virtual criada no **VirtualBox**.

### Inicialização

1. Abra o VirtualBox.
2. Selecione a máquina virtual do projeto.
3. Faça um clone da máquina virtual.
4. Inicie o clone da máquina virtual.
5. Faça login utilizando um usuário que não seja `root` (`fsayuri-`).

A máquina foi configurada sem ambiente gráfico, portanto toda a administração é realizada pelo terminal.

A máquina virtual e seu disco virtual não fazem parte do repositório Git.

### Acesso por SSH

O serviço SSH dentro da máquina virtual está configurado para utilizar a porta `4242`.

Para acessar a máquina diretamente pelo seu endereço IP:

```bash
ssh <usuario>@<ip-da-maquina> -p 4242
```

Também foi configurado no VirtualBox um redirecionamento de porta entre o computador host e a máquina virtual.

Nesse caso, a porta `2222` do host é redirecionada para a porta `4242` da máquina virtual.

Assim, o acesso pode ser realizado com:

```bash
ssh <usuario>@localhost -p 2222
```

## Recursos

Os seguintes materiais foram utilizados como referência durante o desenvolvimento e a revisão do projeto:

- Subject oficial do Born2beRoot, disponibilizado pela 42.
- [Documentação oficial do Debian](https://www.debian.org/doc/)
- [Debian Reference](https://www.debian.org/doc/manuals/debian-reference/)
- [Debian Administrator's Handbook](https://www.debian.org/doc/manuals/debian-handbook/)
- [Documentação do `sshd_config` no Debian](https://manpages.debian.org/trixie/openssh-server/sshd_config.5.en.html)
- [Documentação do UFW no Debian](https://manpages.debian.org/trixie/ufw/ufw.8.en.html)
- [Documentação oficial do AppArmor](https://apparmor.net/)
- [Manual do Oracle VirtualBox](https://www.virtualbox.org/manual/)

### Uso de IA

Ferramentas de IA foram utilizadas como apoio ao processo de aprendizagem e documentação do projeto.

O uso ocorreu principalmente para esclarecer conceitos de administração de sistemas, revisar comandos e configurações, interpretar requisitos do subject e organizar as explicações presentes neste README.

A IA também foi utilizada durante a revisão de tópicos como virtualização, SSH, UFW, `sudo`, LVM, `cron`, AppArmor e o script `monitoring.sh`.

As configurações foram aplicadas e testadas diretamente na máquina virtual, e os conceitos utilizados foram revisados para garantir a compreensão do funcionamento do sistema.
