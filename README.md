# Aula 2 - IaC com Vagrant e Ansible [1º DESAFIO]
![Banner](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/banner.png)

Primeiro desafio do BootCamp DevOps do Instituto Atlântico.

Neste repositório, **explorei** o uso de **Vagrant** e **Ansible** para criar e gerenciar máquinas virtuais. Também **realizei** o deploy do site "Mundo Invertido".

---

## Sumário

-   [Pré-requisitos](#pré-requisitos)
-   [Passo a passo](#passo-a-passo)
-   [Desafio Opcional](#desafio-opcional)
---

## Pré-requisitos

-   [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
-   [Vagrant](https://developer.hashicorp.com/vagrant/downloads?product_intent=vagrant)
-   [Visual Studio Code](https://code.visualstudio.com)

---

## Passo a passo

1.  **Clonando o repositório:**
    ```bash
    git clone https://gitlab.com/dvp2025-2/aula-2-iac-com-vagrant-e-ansible.git
    cd aula-2-iac-com-vagrant-e-ansible
    ```


2.  **Inicialize o Vagrant:**
    ```bash
    vagrant init
    ```
    Isso criará o arquivo `Vagrantfile`.


3.  **Editando o `Vagrantfile`:**
    Abra o `Vagrantfile` no VS Code:
    ```bash
    code .
    ```
    Apague todo o conteúdo e substitua por:
    ```ruby
    Vagrant.configure("2") do |config|
        config.vm.box = "ubuntu/focal64"
        config.vm.network "forwarded_port", guest: 80, host: 8080
        config.vm.provider "virtualbox" do |vb|
          vb.memory = "1024"
          vb.cpus = 1
          vb.name = "nginx - webserver"
        end
        config.vm.provision "ansible_local" do |ansible|
          ansible.playbook = "playbook.yml"
        end
    end
    ```
    > **Nota:** Este `Vagrantfile` declara como a VM será criada e configurada com Ansible.

4.  **Crie o `playbook.yml` do Ansible:**
    No VS Code, crie um arquivo chamado `playbook.yml` e adicione:
    ```yaml
    ---
    - hosts: all
      become: yes
      tasks:
        - name: Atualiza o cache do apt
          apt:
            update_cache: yes
          tags:
            - packages
        - name: Instala o Nginx
          apt:
            name: nginx
            state: present
          tags:
            - packages
        - name: Copia a página web para o diretório do Nginx
          copy:
            src: files/
            dest: /var/www/html
            owner: www-data
            group: www-data
            mode: '0644'
          notify:
            - Reiniciar Nginx
      handlers:
        - name: Reiniciar Nginx
          service:
            name: nginx
            state: restarted
    ```
    > **Nota:** Este `playbook.yml` define o estado desejado do sistema na VM.

5.  **Inicie o provisionamento:**
    ```bash
    vagrant up
    ```
    ![Subindo Vagrant](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/vagrant-up.png)
    ![Subindo Vagrant](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/vagrant-up2.png)
    ![Virtual Box](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/virtualbox.png)

6.  **Acesse o site:**
    Após o provisionamento, acesse no navegador: [http://localhost:8080](http://localhost:8080)
    O resultado é este:
    ![Site Deployed](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/site-deployed.png)
    Funcionalidade do site:
    ![Site Running](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/site-running.png)

7.  **Desprovisionar a máquina virtual:**
    ```bash
    vagrant destroy
    ```
    ![Vagrant Destroy](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/vagrant-destroy.png)

---

## Desafio Opcional

-   **Usei** o módulo `template` do Ansible para copiar e alterar o `index.html` em vez do módulo `copy`.
    -   **Criei** `files/index.html.j2`:
        ```bash
        code files/index.html.j2
        ```
    -   Consultei a documentação do módulo `template`: [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
    
  ![Template Ansible](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/template.png)
-   Após configurar o módulo `template`, **provisionei** novamente:
    ```bash
    vagrant provision
    ```
![Desafio Provision](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/desafio-provision.png)    
-   **Acessando** o site novamente e **verificando** se a alteração foi aplicada: [http://localhost:8080](http://localhost:8080)
![Mundo Invertido](https://github.com/leonildolinck/Avanti-DevOps-Desafio-1/blob/main/screenshots/desafio.png)

---

## Projeto individual

Este projeto foi desenvolvido com base em conceitos aprendidos durante o bootcamp, visando simplicidade e eficiência. Para eventuais dúvidas ou melhorias, sinta-se à vontade para entrar em contato.

### Contato:
- **E-mail**: leonildolinck@gmail.com 
- **Discord**: leonildo
