### 1. Создание и заполнение файла со списком целей для сканирования
cd ~/lab-ansible
nano targets.txt

student.psu.ru
etis.psu.ru
www.psu.ru


### 2. Создание плейбука
nano playbook_nmap.yml


### 3. Заполнение плейбука
---
- hosts: webservers
  become_method: sudo
  remote_user: runner
  become: true

  tasks:
    - name: Install NMAP
      ansible.builtin.apt:
        name: nmap
        state: present

    - name: Copy nmap targets list
      ansible.builtin.copy:
        src: ./targets.txt
        dest: ~/targets.txt
        force: yes
        mode: '0644'

    - name: Scan
      ansible.builtin.shell: "nmap -iL ~/targets.txt -p 80 -oG -"
      register: nmap_result

    - name: Scan results
      ansible.builtin.debug:
        msg: "{{ nmap_result.stdout_lines }}"


### 4. Запуск плейбука
ansible-playbook playbook_nmap.yml -i inventory.ini