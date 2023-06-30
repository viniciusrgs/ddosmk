**Basica PROTEÇÃO DDOS MK**


--/ip firewall filter
add action=accept chain=input comment="Accept established" \
    connection-state=established
add action=accept chain=input comment="Accept winbox" dst-port=8291 protocol=\
    tcp
add action=accept chain=input comment="Controle de pings 1/2" limit=\
    50/5s,2:packet protocol=icmp
add action=drop chain=input comment="Controle de pings bloqueia ICMP Flood 2/2" \
    protocol=icmp
add action=add-src-to-address-list address-list=DDos_blacklist \
    address-list-timeout=1d chain=input comment="Prote\E7\E3o DDos 1/5" \
    connection-limit=32,32 protocol=tcp
add action=tarpit chain=input comment="Prote\E7\E3o DDos 2/5" connection-limit=\
    3,32 protocol=tcp src-address-list=DDos_blacklist
add action=jump chain=forward comment=\
    "Prote\E7\E3o DDos 3/5 (ativar somente em caso de DDos Flood)" \
    connection-state=new disabled=yes jump-target=SYN-Protect protocol=tcp \
    tcp-flags=syn
add action=accept chain=SYN-Protect comment=\
    "Prote\E7\E3o DDos 4/5 prote\E7\E3o SYN Flood" connection-state=new limit=\
    400,5:packet protocol=tcp tcp-flags=syn
add action=drop chain=SYN-Protect comment=\
    "Prote\E7\E3o DDos 5/5 prote\E7\E3o SYN Flood" connection-state=new \
    protocol=tcp tcp-flags=syn
add action=drop chain=input comment="Drop input all wan" in-interface-list=WAN--
