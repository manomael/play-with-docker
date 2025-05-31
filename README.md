# play-with-docker

O Play With Docker oferece uma Máquina Virtual Alpine Linux gratuita no navegador, permitindo que você crie e execute contêineres Docker e crie clusters usando o Docker Swarm Mode. Por baixo dos panos, ele utiliza DIND (Docker-in-Docker) para simular múltiplos VMs/PCs.

Uma versão ao vivo está disponível em: [http://play-with-docker.com/](http://play-with-docker.com/)

## Funcionalidades

- Máquina Virtual Alpine Linux gratuita na nuvem
- Crie e execute contêineres Docker
- Crie clusters com Docker Swarm Mode
- Simula múltiplos VMs/PCs com DIND (Docker-in-Docker)

## Índice

- [Requisitos](#requisitos)
- [Desenvolvimento](#desenvolvimento)
  - [Encaminhamento de Porta](#encaminhamento-de-porta)
  - [Construindo a Imagem DIND](#construindo-a-imagem-dind)
- [FAQ](#faq)
- [Contribuindo](#contribuindo)
- [Licença](#licenca)

## Requisitos

É necessário Docker 1.13+.

O daemon do Docker deve estar rodando em Swarm Mode porque o PWD utiliza redes overlay anexáveis. Para habilitar o Swarm Mode, execute `docker swarm init` no daemon de destino.

Também é necessário carregar manualmente o módulo do kernel IPVS, pois o daemon não o carregará automaticamente quando os swarms são criados no DIND. Execute o seguinte comando:
```bash
sudo modprobe xt_ipvs
```

## Desenvolvimento

Para começar com o desenvolvimento:

1. Certifique-se de que o daemon do Docker esteja rodando na sua máquina.
2. Baixe a imagem DIND: `docker pull franela/dind`.
3. Instale o Go 1.7.1+ (por exemplo, usando `brew` no macOS ou o gerenciador de pacotes do seu sistema).
4. Instale o [dep](https://github.com/golang/dep) para gerenciamento de dependências e execute `dep ensure` para baixar as dependências.
5. Inicie o PWD como um contêiner usando `docker-compose up`.
6. Abra [http://localhost](http://localhost) no seu navegador e clique em "Nova Instância".

**Observações:**

*   Existe um limite fixo de 5 playgrounds Docker por sessão. As sessões são excluídas após 4 horas.
*   Para substituir a versão ou imagem DIND, defina a variável de ambiente `DIND_IMAGE` (por exemplo, `DIND_IMAGE=franela/docker<versao>-rc:dind`). Note que apenas imagens DIND da [franela](https://hub.docker.com/r/franela/) são compatíveis, não imagens `dind` padrão.

### Encaminhamento de Porta

Para que o encaminhamento de porta funcione corretamente durante o desenvolvimento, `*.localhost` deve resolver para `127.0.0.1`. Isso garante que, ao acessar uma URL como `pwd10-0-0-1-8080.host1.localhost`, você seja encaminhado corretamente para o seu servidor PWD local.

Você pode conseguir isso configurando um servidor `dnsmasq` (que também pode rodar em um contêiner Docker) com a seguinte configuração:

```
address=/localhost/127.0.0.1
```

Lembre-se de configurar o DNS padrão do seu computador para usar o servidor dnsmasq para resolução.

### Construindo a Imagem DIND

Se você precisar modificar a imagem DIND:

1. Faça suas alterações no arquivo `Dockerfile.dind`.
2. Construa a imagem usando o comando:
   ```bash
   docker build --build-arg docker_storage_driver=vfs -f Dockerfile.dind -t franela/dind .
   ```

## FAQ

### Como posso me conectar a uma porta publicada do mundo exterior?

Para acessar seus serviços de fora, use o seguinte padrão de URL: `http://ip<hifen-ip>-<id_sessao>-<porta>.direct.labs.play-with-docker.com` (por exemplo, `http://ip-2-135-3-b8ir6vbg5vr00095iil0-8080.direct.labs.play-with-docker.com`).

### Por que o PWD está rodando nas portas 80 e 443? Posso mudar isso?

Não, o PWD precisa rodar nessas portas para que a resolução de DNS funcione corretamente. Agradecemos ideias ou sugestões sobre como melhorar isso.

## Contribuindo

Agradecemos contribuições para o Play With Docker! Se você gostaria de contribuir, siga estas diretrizes gerais:

1.  Faça um fork do repositório.
2.  Crie uma nova branch para suas alterações.
3.  Faça suas alterações, garantindo que sejam bem testadas e documentadas.
4.  Envie um pull request para revisão.

Se você encontrar quaisquer bugs ou tiver solicitações de funcionalidades, abra uma issue em nossa [página de Issues do GitHub](https://github.com/play-with-docker/play-with-docker/issues).

## Licença

Este projeto está licenciado sob a Licença Apache 2.0. Consulte o arquivo [LICENSE](LICENSE) para obter detalhes.
