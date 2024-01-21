# Ansible

TABS, SPATIES, ENTERS heel belangrijk!!!!
run playbook:

    ansible-playbook "playbook_to_run"

## Important folders

## Assignments

see /etc/scripts
ApacheVirtualHostsConig.yml

    ---
    - name: Configure Apache Virtual Hosts
    hosts: localhost
    become: yes
    tasks:
        - name: Install Apache
        apt:
            name: apache2
            state: latest

        - name: Copy Virtual Host Files
        copy:
            src: "{{ item.src }}"
            dest: "/etc/apache2/sites-available/{{ item.dest }}"
            owner: root
            group: root
            mode: '0644'
        loop:
            - { src: 'virtualHostConfig/000-default.conf', dest: '000-default.conf' }
            - { src: 'virtualHostConfig/www1.ferre-verheyen.sasm.uclllabs.be.conf', dest: 'www1.ferre-verheyen.sasm.uclllabs.be.conf' }
            - { src: 'virtualHostConfig/www2.ferre-verheyen.sasm.uclllabs.be.conf', dest: 'www2.ferre-verheyen.sasm.uclllabs.be.conf' }
            - { src: 'virtualHostConfig/secure.ferre-verheyen.sasm.uclllabs.be.conf', dest: 'secure.ferre-verheyen.sasm.uclllabs.be.conf' }
            - { src: 'virtualHostConfig/supersecure.ferre-verheyen.sasm.uclllabs.be.conf', dest: 'supersecure.ferre-verheyen.sasm.uclllabs.be.conf' }

        - name: Enable Virtual Hosts
        shell: a2ensite "{{ item }}"
        loop:
            - '000-default'
            - 'www1.ferre-verheyen.sasm.uclllabs.be'
            - 'www2.ferre-verheyen.sasm.uclllabs.be'
            - 'secure.ferre-verheyen.sasm.uclllabs.be'
            - 'supersecure.ferre-verheyen.sasm.uclllabs.be'

        - name: Copy Web Files
        copy:
            src: "virtualHostConfig/var/www/"
            dest: "/var/www/"
            owner: www-data
            group: www-data
            recurse: yes

        - name: Restart Apache
        service:
            name: apache2
            state: restarted

freeRadius.yml

    ---
    - hosts: localhost
    become: yes
    tasks:
        - name: Install FreeRADIUS
        apt:
            name: freeradius
            state: present
            update_cache: yes

        - name: Ensure FreeRADIUS starts on boot
        systemd:
            name: freeradius
            enabled: yes

        - name: Add user 'check' to FreeRADIUS
        lineinfile:
            path: /etc/freeradius/3.0/users
            line: 'check Cleartext-Password := "Ch3ck"'
            create: yes

        - name: Enable control socket in FreeRADIUS config
        blockinfile:
            path: /etc/freeradius/3.0/radiusd.conf
            block: |
            control-socket {
                path = ${run_dir}/radiusd.sock
                user = freerad
                group = freerad
                mode = rw-------
            }
            marker: "# {mark} ANSIBLE MANAGED BLOCK for control-socket"

        - name: Set up detailed logging for user 'check'
        blockinfile:
            path: /etc/freeradius/3.0/sites-enabled/default
            block: |
            if (User-Name == "check") {
                detail
            }
            marker: "# {mark} ANSIBLE MANAGED BLOCK for user 'check' logging"
            insertafter: "authorize {"

        - name: Open port 1812 for RADIUS
        iptables:
            chain: INPUT
            protocol: udp
            destination_port: 1812
            jump: ACCEPT

## Handy stuff
