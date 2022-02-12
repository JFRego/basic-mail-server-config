Exim4 and courier config debian based


- first you need to install exim4 and courier packages;  

        apt install exim4-daemon-heavy sasl2-bin courier-pop courier-imap -y
        
- next place your cerfiticates signed as server type inside;

        .crt in /etc/ssl/certs
        .key in /etc/ssl/private
        
- then make a chain file with your cert and key and place it inside /etc/private;

        cat .crt .key > chained.pem
                
                note: change permissions and group;
                
                        chmod 640 chained.pem
                        chgrp ssl-cert chained.pem
                        
- after that add ssl-cert group;

        addgroup --system ssl-cert
        
- next add this 3 following users;

        adduser Debian-exim ssl-cert
        adduser Debian-exim sasl
        adduser courier ssl-cert
        
- now open /etc/courier/imapd-ssl and /etc/courier/pop3d-ssl and edit the following line on both;

        TLS_CERTFILE=/etc/ssl/private/chained.pem
        
- next you need to configure exim4, for that run;

        dpkg-reconfigure exim4-config
        
                1- type your server domain (inova.pt)
                2- blank
                3- inova.pt
                4- blank
                5- blank
                6- select NO
                7- choose Maildir format in home directory
                8- select NO
                
- now open /etc/default/saslauthd and change no to yes ins the following line;

        START=yes
        
- then open /etc/exim4/exim4.conf.template, add and uncomment the following sections;

        add:    MAIN_TLS_ENABLE=yes
                MAIN_TLS_CERTIFICATE=/etc/ssl/certs/mail.inova.crt
                MAIN_TLS_PRIVATEKEY=/etc/ssl/private/mail.inova.key
                
        uncomment:      # Authenticate against local passwords using sasl2-bin
                        # Requires exim_uid to be a member of sasl group, see README.Debian.gz
                        plain_saslauthd_server:
                        driver = plaintext
                        public_name = PLAIN
                        server_condition = ${if saslauthd{{$auth2}{$auth3}}{1}{0}}
                        server_set_id = $auth2
                        server_prompts = :
                        .ifndef AUTH_SERVER_ALLOW_NOTLS_PASSWORDS
                        server_advertise_condition = ${if eq{$tls_in_cipher}{}{}{*}}
                        .endif

                        login_saslauthd_server:
                        driver = plaintext
                        public_name = LOGIN
                        server_prompts = "Username:: : Password::"
                        # don't send system passwords over unencrypted connections
                        server_condition = ${if saslauthd{{$auth1}{$auth2}}{1}{0}}
                        server_set_id = $auth1
                        .ifndef AUTH_SERVER_ALLOW_NOTLS_PASSWORDS
                        server_advertise_condition = ${if eq{$tls_in_cipher}{}{}{*}}
                        .endif   
                        
- next run this following command or create the same file and add the line inside;

        echo "tls_on_connect_ports = 465" > /etc/exim4/exim4.conf.localmacros
        
- now open /etc/default/exim4;

        copy:   -oX 25:587:10025 -oP /run/exim4/exim.pid
        paste abd add 465 port: SMTPLISTENEROPTIONS='-oX 25:465:587:10025 -oP /run/exim4/exim.pid'
        
- then go to /etc/skel and create Maildir directory;

        mkdir Maildir
        
                now inside Maildir make courier directory;
                        
                        maildirmake courier
                        
- after that enable and start the services;

        systemctl enable saslauthd.service exim4.service courier-authdaemon.service courier-imap.service courier-imap-ssl.service courier-pop.service courier-pop-ssl.service 
        systemctl start saslauthd.service exim4.service courier-authdaemon.service courier-imap.service courier-imap-ssl.service courier-pop.service courier-pop-ssl.service
        
- check the status of all them for erros and its done;        
        
        
        
        
