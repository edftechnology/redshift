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

### Configuração manual sem localização automática

Em instalações `Ubuntu`, usar o arquivo `~/.config/redshift.conf` (diretamente dentro de `.config`). A configuração manual evita a consulta de localização pelo `geoclue2`. Para uma sessão X11, criar ou editar o arquivo e substituir os dois valores de exemplo por coordenadas decimais do município desejado. As coordenadas ficam no arquivo local do usuário.

```ini
[redshift]
location-provider=manual
adjustment-method=randr
temp-day=6500
temp-night=4500

[manual]
lat=SUBSTITUIR_PELA_LATITUDE
lon=SUBSTITUIR_PELA_LONGITUDE
```

Não salvar os textos `SUBSTITUIR...` literalmente. Depois de salvar, reiniciar `redshift-gtk` e usar `redshift -p` para conferir os parâmetros calculados, sem alterar a tela. A configuração e os métodos disponíveis são descritos no arquivo de exemplo do projeto upstream [4].

## 6. Solução de problemas (Troubleshooting)

### Diferenciar a interface do ajuste de cor

O ícone do `redshift-gtk` indica que a interface de bandeja está ativa, mas não confirma que o ajuste de cor foi aplicado. A interface inicia um processo `redshift`; se esse processo falhar ao obter localização ou acessar o método gráfico, pode aparecer a janela `Failed to run Redshift`.

Consultar os processos com:

    ```bash
    pgrep -a -x redshift-gtk
    pgrep -a -x redshift
    ```

Se apenas `redshift-gtk` estiver ativo, conferir o erro da janela e o diagnóstico abaixo. Se ambos estiverem ativos, ainda confirmar a saída de `redshift -v -p` e a mudança visual da tela.

### Aviso `dbind-WARNING` e barramento de acessibilidade

A mensagem `Couldn't connect to accessibility bus` indica que o GTK não conseguiu conectar-se ao barramento AT-SPI, usado pelos recursos de acessibilidade. Ela não demonstra, por si só, que o ajuste de cor falhou; esse barramento é separado do funcionamento do `redshift` [7].

Comparar o UID e as variáveis da sessão com o caminho `/run/user/<UID>/` citado no aviso:

    ```bash
    id -u
    printf 'XDG_RUNTIME_DIR=%s\nDBUS_SESSION_BUS_ADDRESS=%s\nXDG_SESSION_TYPE=%s\n' \
      "$XDG_RUNTIME_DIR" "$DBUS_SESSION_BUS_ADDRESS" "$XDG_SESSION_TYPE"
    ```

Uma diferença é uma pista de ambiente de sessão inconsistente, não prova isolada da causa. Não alterar serviços ou variáveis globais sem identificar a origem. Como teste pontual, `NO_AT_BRIDGE=1 redshift-gtk` desativa a ponte AT-SPI para esse processo; não usar se os recursos de acessibilidade forem necessários e não definir globalmente essa variável.

### `Unable to get location from provider`

As mensagens `Waiting for initial location to become available` e `Unable to get location from provider` significam que o provedor `geoclue2` não entregou uma posição ao `redshift`. O ícone pode continuar visível, mas o agendamento automático de dia e noite fica sem a localização necessária.

No `Ubuntu 22.04`, a configuração do `geoclue2` aponta para a API Wi-Fi do Mozilla Location Service; a Mozilla retirou o acesso de terceiros em 2024. Portanto, essa API pode explicar a espera, embora permissões, conectividade ou outros problemas do `geoclue2` também possam causá-la [5][6].

Para não depender de localização automática nem enviar coordenadas a um serviço, usar a configuração manual local descrita na subseção `Configuração manual sem localização automática`. No pacote Ubuntu, salvar o arquivo diretamente em `~/.config/redshift.conf`; não usar `~/.config/redshift/redshift.conf`, pois o perfil AppArmor do pacote pode bloquear esse caminho. Reiniciar o `redshift-gtk` depois de salvar.

### `Could not connect to wayland display`

O `redshift` pode tentar o método `wayland` e depois procurar outro método. A mensagem `Trying next method` indica uma tentativa de fallback; ela não informa se o método seguinte funcionou.

Em uma sessão X11, conferir `XDG_SESSION_TYPE` e `DISPLAY`, listar os métodos disponíveis com `redshift -m list` e definir `adjustment-method=randr` no arquivo `~/.config/redshift.conf`. Em uma sessão Wayland, não forçar `randr`; selecionar um método compatível listado pelo próprio `redshift` e pela sessão gráfica.

### Verificar após corrigir a configuração

1. Sair da instância atual do `redshift-gtk` pelo menu do ícone e iniciá-la novamente para que leia a configuração atualizada.
2. Consultar os processos e os métodos disponíveis:

    ```bash
    pgrep -a -x redshift-gtk
    pgrep -a -x redshift
    redshift -m list
    redshift -l list
    ```

3. Consultar os parâmetros calculados:

    ```bash
    redshift -v -p
    ```

O modo `-p` imprime parâmetros e não deve alterar a tela. Com localização manual, conferir a posição, o período e a temperatura calculada. Se o comando permanecer aguardando o `geoclue2`, interrompê-lo com `Ctrl + C` e revisar se o arquivo `~/.config/redshift.conf` especifica `location-provider=manual`.

4. Opcionalmente, testar diretamente o ajuste da tela. Esse teste altera temporariamente a cor; executar a restauração em seguida:

    ```bash
    redshift -O 4500
    redshift -x
    ```

Depois de confirmado o funcionamento, repetir a ação de duração temporária desejada no menu do `redshift-gtk`.

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

No resultado de `dpkg -l`, `un` indica que o pacote não está instalado e `rc` indica que o programa foi removido, mas restam arquivos de configuração do pacote. `apt remove` conserva esses arquivos; `apt purge` remove também a configuração mantida pelo gerenciador. Escolher `purge` somente se essa remoção for desejada. Configurações pessoais em `~/.config/redshift.conf` não devem ser apagadas automaticamente.

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

[4] REDSHIFT CONTRIBUTORS.
**redshift.conf.sample**.
Disponível em: <https://github.com/jonls/redshift/blob/master/redshift.conf.sample>.
GitHub.
Acessado em: 01/11/2026.

[5] UBUNTU DEVELOPERS.
**Manual: geoclue.conf**.
Disponível em: <https://manpages.ubuntu.com/manpages/jammy/man5/geoclue.5.html>.
Ubuntu Manpages.
Acessado em: 01/11/2026.

[6] MOZILLA.
**Retiring the Mozilla Location Service**.
Disponível em: <https://github.com/mozilla/ichnaea/issues/2065>.
GitHub.
Acessado em: 01/11/2026.

[7] GNOME.
**Launcher for the accessibility bus**.
Disponível em: <https://github.com/GNOME/at-spi2-core/blob/main/bus/README.md>.
GitHub.
Acessado em: 01/11/2026.
