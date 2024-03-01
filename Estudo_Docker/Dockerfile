# O comando FROM baixa do Docker Hub a imagem do ROS 2 com ferramentas desktop completas, versão Humble 
FROM osrf/ros:humble-desktop-full 

# O comando RUN instala o editor de texto nano dentro do imagem gerada
# de modo que quando executar o comando "nano <arquivo_de_texto>", será
# possível ver o conteúdo ou até editar o conteúdo deste arquivo 

RUN apt-get update \
    && apt-get install -y nano \
    && rm -rf /var/lib/apt/lists/* 

# O comando abaixo permite a leitura de dispositivos conectados via serial 
# Atráves do comando pelo pyserial-miniterm, encotramos esp32
RUN apt-get update \
    && apt-get install -y python3 python3-pip \
    && pip3 install pyserial \
    && rm -rf /var/lib/apt/lists/* 

# O comando COPY copia os arquivos que você criou de umas pasta para outra pasta dentro da imagem a ser gerada,
# para isso basta passar para onde queremos que copie para aonde é para ser copiada
# no caso é a pasta de origem é "config" e estamos passando o dado "alguma_coisa", que vale 27, dentro do arquivo "my_config.yaml" para a 
# pasta dentro da imagem, esta pasta se chamará "config_test"

COPY config/ /config_test/

# A sequência de abaixo cria uma usuário denominado ros com id 1000 (mesmo nível de acesso que usário normais)
# Esse comando atribui a variável USERNAME o nome ros
# Esse comando atribui a varáivel USER_ID o ID 1000   
ARG USERNAME=ros         
ARG USER_UID=1000 
ARG USER_GID=$USER_UID   

# O comando abaixo cria usuário com mesmo privilégios que um usuário "normal" do linux
# Além disso, ele cria um diretório (umas pasta) e configura para que programas utilizem normalmente. 
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && mkdir /home/$USERNAME/.config && chown $USER_UID:$USER_GID /home/$USERNAME/.config

# O comando abaixo está configurando o sudo para ser usado dentro do container
# Além disso, fornece ao usuário permissões do nível sudo
# Por fim, também seta que só precisaremos fornecer a senha somente uma vez toda vez que usamos o sudo
RUN apt-get update \
    && apt-get install -y sudo \ 
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && rm -rf /var/lib/apt/lists/*

# O comando abaixo permite utilizar dispositivos seriais, no caso, esp32
RUN usermod -aG dialout ${USERNAME}

# Configurando o ponto de entrada (entrypoint)

# Primeiro, copiamos o entrypoint para a imagem

COPY entrypoint.sh /entrypoint.sh
COPY bashrc /home/${USERNAME}/.bashrc

# Executa o script entrypoint com o terminal do linux, no caso o bash
ENTRYPOINT [ "/bin/bash","/entrypoint.sh" ]

# Passando comandos para o ponto de entrada
CMD ["bash"]


