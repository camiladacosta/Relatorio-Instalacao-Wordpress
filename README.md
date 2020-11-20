Título: Relatório de Instalação do Wordpress
Autor: Camila da costa
Disciplina: Insfraestrutura para Sistemas Web

## Instalação do Wordpress no Ubuntu Server 18.04

#### Introdução:
O WordPress é um CMS (Content Management System), em português, Sistema de Gerenciamento de Conteúdo. Ele é usado para administrar sites, blogs, lojas virtuais, portais de notícia, entre outros tipos de página.
De forma mais específica, tem como missão facilitar a criação e a edição de conteúdos em um site sem a necessidade de usar uma linguagem de programação.

Para realizar o processo de instalação exposto neste relatório é necessário possuir a pilha LAMP com um servidor Web como Linux, Apache, MySQL, e PHP já previamente instalados na máquina.

#### Passo 1: Criar um banco de dados MySQL para o WordPress
Para criar um novo banco é necessario acessar o mysql como superusuário, utilizando o comando:

    $mysql -u root -p
Após ter feito o login no mysql, vamos criar o novo database "wordpress" com o seguinte comando:

    mysql> CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci; 
Em seguida, cadastrar um usuário exclusivo e senha para acessar esse novo banco e também conceder o seu acesso, através do comando:
    
    mysql> GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
Utilize o seguinte comando para atualizar as modificações feitas no Mysql:

    mysql> FLUSH PRIVILEGES;
#### Passo 2: Instalando extensões do PHP
Primeiramente é necessario um conjunto mínimo de extensões para fazer com que o PHP interaja com o Mysql, utilizando o seguinte comando para satisfazer essa condição:
    
    $sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
E então, restartando o servidor para efetivar as modificações:
    
    $sudo systemctl restart apache2
#### Passo 3: Configurando o Apache
Agora foi necessário fazer alguns ajustes na configuração do Apache, começando pelo arquivo "wordpress.conf".

    $sudo nano /etc/apache2/sites-available/wordpress.conf
E adicionando nesse arquivo o caminho para o diretório raiz da instalação do Worpress que foi escolhida como /var/www/wordpress:
    
    <Directory /var/www/wordpress/>
    AllowOverride All
    </Directory>
Em seguida, é necessário habilitar o mod_rewrite para poder usar o recurso de permalink (ou link permanente) do WordPress, com o seguinte comando:

    $sudo a2enmod rewrite
Executando o seguinte comando para verificar se há algum erro no arquivo que foi alterado:

        $sudo apache2ctl configtest
É necessário que apareça a mensagem "Syntax OK" para prosseguir, caso isso ocorra é só restartar o servidor para efetivar as alterações:

    sudo systemctl restart apache2
#### Passo 4: Download do WordPress
Altere para um diretório gravável como:

    $cd /tmp
E inicie o download do arquivo comprimido do Wordpress digitando:

    $curl -O https://wordpress.org/latest.tar.gz
Em seguida, extraia o conteúdo do arquivo para criar a estrutura de diretórios do Wordpress:

    $tar xzvf latest.tar.gz
Mova esses arquivos temporariamente para a raiz de documentos.Mas antes é necessário adicionar um arquivo de .htaccess fictício para que esteja disponível para o Wordpress.

Crie o arquivo com:

    $touch /tmp/wordpress/.htaccess
Depois copie o arquivo de configuração, com o seguinte comando:

    $cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
Também crie o diretório "upgrade", para evitar possiveis problemas futuros relacionados a permissoes:

    $mkdir /tmp/wordpress/wp-content/upgrade
Agora, copie todo o conteúdo do diretório para a raiz de documentos do Wordpress, com o seguinte comando:

    $sudo cp -a /tmp/wordpress/. /var/www/wordpress
#### Passo 5: Configurando o diretório Wordpress
Antes de tudo é necessário configurar as permissões dos usuários e grupos sobre os arquivos. Atualize as propriedades com:

    $sudo chown -R www-data:www-data /var/www/wordpress
Em seguida, defina as permissões corretas sobre os diretórios e arquivos do diretório, com:

    $sudo find /var/www/wordpress/ -type d -exec chmod 750 {} \;
    $sudo find /var/www/wordpress/ -type f -exec chmod 640 {} \;
Agora é necessário ajustar algumas chaves secretas para dar segurança para a instalação. O Wordpress oferece um gerador seguro desses valores que são usados internamente. Para ter acesso a eles, digite:

    $curl -s https://api.wordpress.org/secret-key/1.1/salt/
Copie todos os valores das chaves que aparecerem e cole diretamente no arquivo de configuração "wp-config" substituindo as linhas que estavam lá, utilize o seguinte comando para editar as chaves:

    $sudo nano /var/www/wordpress/wp-config.php
Em seguida, precisa-se modificar algumas configurações de conexão do banco de dados no início do arquivo "wp-config". Ajustando o nome do banco de dados, o usuário e a senha associada que foram configuradas.

Apenas alterando os dados como no exemplo a seguir:

    define('DB_NAME', 'wordpress');
    /** MySQL database username */
    define('DB_USER', 'wordpressuser');
    /** MySQL database password */
    define('DB_PASSWORD', 'password');
#### Passo 6: Concluindo a instalação
Verifique o endereço IP de sua maquina com o comando:

    $ifconfig
E no seu navegador Web, o digite como no exemplo:

    https://127.0.0.1/wordpress
Conclua as configurações restantes pela interface mesmo, adicionando os dados para o seu usuário e senha e assim conseguindo acesso completo ao Wordpress.

    
    
    
    









