# Como instalar/configurar/usar o `redshift` no `Linux Ubuntu`

## Resumo

Guia para instalar, verificar, utilizar e remover o `redshift` e a interface gráfica `redshift-gtk` no `Linux Ubuntu`, usando os pacotes oficiais da distribuição. Inclui diagnóstico do aviso `dbind-WARNING` sem presumir que ele impeça o funcionamento.

## _Abstract_

_A guide to install, verify, use, and remove `redshift` and its graphical interface `redshift-gtk` on `Linux Ubuntu` using distribution packages. It also covers the `dbind-WARNING` without assuming that it prevents the application from working._

## Descrição

### `redshift`

O `redshift` ajusta a temperatura de cor da tela conforme o horário e a localização. O pacote `redshift-gtk` fornece uma interface gráfica e um ícone de área de notificação. A disponibilidade do ícone depende do ambiente gráfico; instalar o pacote não inicia automaticamente o ajuste da tela.

## Pré-requisitos

- Usar uma versão do `Linux Ubuntu` com repositório `universe` habilitado.
- Ter acesso a uma sessão gráfica compatível com o método de ajuste de tela do sistema.
- Ter permissão administrativa para usar `sudo` e conexão à internet para instalar os pacotes.
- Para localização automática, permitir que o provedor de localização funcione. A alternativa é configurar coordenadas manualmente, sem compartilhar a localização em comandos enviados a terceiros.

## 1. Abrir o `Terminal Emulator`

1. Abrir o `Terminal Emulator`. Você pode fazer isso pressionando:

```text
Ctrl + Alt + T
```

2. Certifique-se de que seu sistema esteja limpo e atualizado.

    2.1 Limpar o `cache` do gerenciador de pacotes `apt`. Especificamente, ele remove todos os arquivos de pacotes (`.deb`) baixados pelo `apt` e armazenados em `/var/cache/apt/archives/`. Digite o seguinte comando:

    ```bash
    sudo apt clean
    ```

    2.2 Remover pacotes `.deb` antigos ou duplicados do `cache` local. É útil para liberar espaço, pois remove apenas os pacotes que não podem mais ser baixados (ou seja, versões antigas de pacotes que foram atualizados). Digite o seguinte comando:

    ```bash
    sudo apt autoclean
    ```

    2.3 Remover pacotes que foram automaticamente instalados para satisfazer as dependências de outros pacotes e que não são mais necessários. Digite o seguinte comando:

    ```bash
    sudo apt autoremove -y
    ```

    2.4 Buscar as atualizações disponíveis para os pacotes que estão instalados em seu sistema. Digite o seguinte comando e pressione `Enter`:

    ```bash
    sudo apt update
    ```

    2.5 **Corrigir pacotes quebrados**: Isso atualizará a lista de pacotes disponíveis e tentará corrigir pacotes quebrados ou com dependências ausentes:

    ```bash
    sudo apt --fix-broken install
    ```

    2.6 Limpar o `cache` do gerenciador de pacotes `apt` novamente:

    ```bash
    sudo apt clean
    ```

    2.7 Para ver a lista de pacotes a serem atualizados, digite o seguinte comando e pressione `Enter`:

    ```bash
    sudo apt list --upgradable
    ```

    2.8 Realmente atualizar os pacotes instalados para as suas versões mais recentes, com base na última vez que você executou `sudo apt update`. Digite o seguinte comando e pressione `Enter`:

    ```bash
    sudo apt full-upgrade -y
    ```

Os comandos de manutenção acima reproduzem o modelo, mas não são necessários para instalar o `redshift`. Em particular, `sudo apt autoremove -y` pode remover pacotes que deixaram de ser classificados como dependências e `sudo apt full-upgrade -y` pode atualizar ou remover vários pacotes do sistema. Revisar as alterações propostas e não executá-los automaticamente.

## 3. Instalação via `apt`

No `Ubuntu 22.04 LTS (Jammy)`, os pacotes `redshift` e `redshift-gtk` estão no componente `universe`. Instalar ambos para obter o utilitário de linha de comando e a interface gráfica.

## 3.1 Código completo para instalar

1. Abrir o `Terminal Emulator` pelo atalho `Ctrl + Alt + T` (atalho de teclado; não digitar no terminal).
2. Copiar e colar o bloco único abaixo. O `apt` pode solicitar confirmação e a senha administrativa.

    ```bash
    sudo apt update
    sudo apt install redshift redshift-gtk -y
    redshift -V
    redshift -h
    ```

## 4. Verificação da instalação

1. Consultar a origem e a versão instaladas:

    ```bash
    apt policy redshift redshift-gtk
    dpkg-query -W -f='${db:Status-Abbrev} ${Package} ${Version}\n' redshift redshift-gtk
    ```

2. Consultar a versão e as opções do utilitário:

    ```bash
    redshift -V
    redshift -h
    ```

O resultado esperado é que `apt policy` mostre a versão instalada e a origem do repositório, e que `dpkg-query` indique estado instalado (`ii`). Não presumir que o ajuste de cor esteja ativo apenas porque os pacotes estão instalados.

## 5. Configuração e utilização

- Iniciar a interface gráfica pelo menu de aplicativos ou executar `redshift-gtk` em uma sessão gráfica. O ícone pode aparecer na área de notificação.
- Consultar processos com `pgrep -a -x redshift` e `pgrep -a -x redshift-gtk`. Ausência de saída significa que não há processo com aquele nome exato no momento da consulta.
- Consultar `redshift -p` para imprimir parâmetros atuais. Com localização automática, esse comando pode aguardar o provedor de localização; interromper com `Ctrl + C` se permanecer aguardando.
- Aplicar temporariamente uma temperatura e brilho manuais com `redshift -O 4500 -b 0.7`. Esse comando altera a imagem da tela. Restaurar a correção de cor com `redshift -x`.
- Verificar a ajuda local com `redshift -h` e o manual com `man redshift`.

## 6. Diagnóstico do aviso de acessibilidade GTK

A mensagem `dbind-WARNING: Couldn't connect to accessibility bus` indica que a ponte de acessibilidade GTK não conseguiu conectar-se ao barramento AT-SPI indicado no aviso. Por si só, o aviso não prova que o `redshift-gtk` falhou; confirmar separadamente se o processo inicia e se a interface funciona.

1. Consultar os processos e os identificadores da sessão:

    ```bash
    pgrep -a -x redshift
    pgrep -a -x redshift-gtk
    id -u
    printf 'XDG_RUNTIME_DIR=%s\nDBUS_SESSION_BUS_ADDRESS=%s\nXDG_SESSION_TYPE=%s\n' \
      "$XDG_RUNTIME_DIR" "$DBUS_SESSION_BUS_ADDRESS" "$XDG_SESSION_TYPE"
    ```

2. Comparar o número em `/run/user/<UID>/` citado no aviso com a saída de `id -u` e `XDG_RUNTIME_DIR`. Uma diferença é uma pista para investigar o ambiente herdado pelo processo, não confirmação isolada de erro. Não alterar variáveis globais nem serviços do sistema sem identificar a sessão que inicia o programa.

3. Como teste opcional por processo, executar `NO_AT_BRIDGE=1 redshift-gtk`. Isso desativa a ponte AT-SPI para esse processo; não usar se os recursos de acessibilidade dessa ponte forem necessários. Fechar a instância de teste pela interface ou interromper o processo no terminal.

4. Para separar o aviso da capacidade de ajuste, executar os comandos de teste de cor da seção anterior somente quando for aceitável alterar temporariamente a imagem e executar `redshift -x` para restaurá-la.

No ambiente desta verificação, `id -u` retornou `1000`, `XDG_RUNTIME_DIR` apontou para `/run/user/1000` e `redshift-gtk --help` reproduziu um aviso citando `/run/user/115`. Essa discrepância foi observada, mas sua causa não foi determinada. `redshift -p` aguardou localização por 12 segundos e foi encerrado pelo limite de tempo; portanto, não foi validado o estado de ajuste da tela.

## 7. Desinstalação

1. Conferir se os pacotes estão instalados e qual repositório os fornece:

    ```bash
    apt policy redshift redshift-gtk
    dpkg-query -W -f='${db:Status-Abbrev} ${Package} ${Version}\n' redshift redshift-gtk 2>/dev/null || true
    ```

2. Remover os pacotes sem apagar, por padrão, arquivos de configuração do sistema ou arquivos pessoais:

    ```bash
    sudo apt remove redshift redshift-gtk -y
    ```

3. Confirmar o estado posterior:

    ```bash
    dpkg -l redshift redshift-gtk
    ```

No resultado de `dpkg -l`, `un` indica que o pacote não está instalado e `rc` indica que o programa foi removido, mas restam arquivos de configuração do pacote. `apt remove` conserva esses arquivos; `apt purge` remove também a configuração mantida pelo gerenciador. Escolher `purge` somente se essa remoção for desejada. Configurações pessoais em `~/.config/redshift` não devem ser apagadas automaticamente.

4. Opcionalmente, inspecionar as dependências órfãs antes de qualquer remoção:

    ```bash
    apt autoremove
    ```

Revisar a lista proposta antes de confirmar. A operação pode remover dependências compartilhadas com outros programas; não executar automaticamente. A remoção não garante reversão integral de alterações do sistema.

## 7.1 Código completo para desinstalar

1. Abrir o `Terminal Emulator` pelo atalho `Ctrl + Alt + T` (atalho de teclado; não digitar no terminal).
2. Copiar e colar o bloco único abaixo. Conferir a origem e o estado antes de remover.

    ```bash
    apt policy redshift redshift-gtk
    dpkg-query -W -f='${db:Status-Abbrev} ${Package} ${Version}\n' redshift redshift-gtk 2>/dev/null || true
    sudo apt remove redshift redshift-gtk -y
    dpkg -l redshift redshift-gtk
    ```

Para eliminar configurações do sistema gerenciadas pelo pacote, substituir `remove` por `purge` conscientemente. Não incluir `apt autoremove` sem revisar previamente a lista que ele propõe.

## Compatibilidade

Os pacotes consultados são os do `Linux Ubuntu 22.04 LTS (Jammy)`, versão `1.12-4.2ubuntu1`, no componente `universe`. O suporte ao ajuste da tela depende do método gráfico disponível na sessão; a instalação dos pacotes não garante compatibilidade com todo compositor ou sessão gráfica.

## Licença

Este repositório inclui o arquivo `LICENSE.txt`. A licença do projeto do `redshift` é indicada no repositório oficial upstream; consultar essa fonte para os termos do aplicativo.

## Contato e suporte

Para suporte sobre os pacotes, consultar a página do pacote no `Ubuntu` e, para o aplicativo, o repositório upstream do `redshift`. Ao relatar problemas, incluir versão, ambiente gráfico e mensagens relevantes, removendo dados pessoais e coordenadas de localização.

## Referências

[1] UBUNTU DEVELOPERS.
**Package: redshift**.
Disponível em: <https://packages.ubuntu.com/jammy/redshift>.
Ubuntu Packages.
Acessado em: 31/10/2026.

[2] UBUNTU DEVELOPERS.
**Package: redshift-gtk**.
Disponível em: <https://packages.ubuntu.com/jammy/redshift-gtk>.
Ubuntu Packages.
Acessado em: 31/10/2026.

[3] LUND STEFFENSEN, Jon.
**Redshift**.
Disponível em: <https://github.com/jonls/redshift>.
GitHub.
Acessado em: 31/10/2026.
