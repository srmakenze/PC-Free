# PC-Free: configuração executável

O repositório tinha instruções com YAML dentro do README, mas não tinha o arquivo de execução. Este complemento acrescenta `windows.yml`, credenciais de exemplo e armazenamento persistente em `/storage`, conforme o projeto oficial.

## Requisitos

Docker Engine em host Linux com KVM e /dev/net/tun acessíveis, no mínimo 6 GB de RAM disponíveis para a VM e espaço para o disco virtual e o instalador. Docker Desktop em Windows 11 exige virtualização aninhada disponível ao container. Verifique esse requisito antes de iniciar.

GitHub armazena o código. Um Codespace está sujeito a cotas, timeout e disponibilidade de KVM; esta configuração não o transforma em Windows gratuito ilimitado nem em hospedagem 24/7.

## Preparar e iniciar

1. Copie `.env.example` para `.env`.
2. Edite `WINDOWS_PASSWORD` com uma senha forte. Não publique esse arquivo.
3. No host Linux, verifique `test -r /dev/kvm && test -w /dev/kvm && test -e /dev/net/tun`.
4. Confira `docker info` e `docker compose version`.
5. Execute `docker compose -f windows.yml config --quiet` para validar.
6. Execute `docker compose -f windows.yml up -d`.
7. Consulte `docker compose -f windows.yml logs -f windows`.
8. Abra http://127.0.0.1:8006. A primeira execução baixa e instala o Windows.

O RDP local usa 127.0.0.1:13389. As portas ficam vinculadas ao loopback. Para acesso externo, use um túnel autenticado ou VPN; não publique o visualizador sem autenticação.

## Parar e retomar

`docker compose -f windows.yml stop` encerra com tempo para o Windows desligar. `docker compose -f windows.yml start` retoma. O volume windows-data persiste. Não use `down -v` para manutenção: isso remove o disco.

## Diagnóstico

- Falta /dev/kvm: o host não disponibiliza a virtualização necessária. Reiniciar workflows não corrige isso.
- Sem memória: reduza a RAM da VM ou escolha um host maior.
- Download demorado: consulte os logs e confira espaço/rede.
- Porta ocupada: altere apenas a porta à esquerda no mapeamento.
- Windows exige licença válida. O projeto não fornece licença.

A configuração foi revisada contra [dockur/windows](https://github.com/dockur/windows). Ainda precisa de teste de boot no host que será usado. A tag latest acompanha o upstream; fixe uma versão/digest após validar em produção.
