========================
nmcli module for ansible
========================

Gather information about networkmanager devices using nmcli.


Example
=======

.. code:: yaml

    ---
    # playbook.yml
    - hosts: 127.0.0.1
      connection: local
      tasks:
        - name: get custom facts
          action: nmcli


.. code::

    $ ansible-playbook playbook.yml -v

    PLAY [127.0.0.1] **************************************************************

    GATHERING FACTS ***************************************************************
    ok: [127.0.0.1]

    TASK: [get custom facts] ******************************************************
    changed: [127.0.0.1] => {"ansible_facts": {"ansible_networkmanager":
    {"devices": {"eth0": {"CONNECTION": "not connected", "DEVICE": "eth0",
    "DRIVER": "r8169", "FIRMWARE-MISSING": "no", "HWADDR": "XX:XX:XX:XX:XX:XX",
    "IP-IFACE": "", "NM-MANAGED": "yes", "PRODUCT": "RTL8111/8168B PCI Express
    Gigabit Ethernet controller", "REASON": "2 (Device is now managed)",
    "STATE": "20 (unavailable)", "TYPE": "802-3-ethernet", "UDI":
    "/sys/devices/pci0000:00/0000:00:1c.5/0000:09:00.0/net/eth0", "VENDOR":
    "Realtek Semiconductor Co., Ltd.", "state": "unavailable", "type":
    "802-3-ethernet"}, "wlan0": {"CONNECTION":
    "/org/freedesktop/NetworkManager/ActiveConnection/0", "DEVICE": "wlan0",
    "DRIVER": "rtl8192ce", "FIRMWARE-MISSING": "no", "HWADDR":
    "XX:XX:XX:XX:XX:XX", "IP-IFACE": "wlan0", "NM-MANAGED": "yes", "PRODUCT":
    "RTL8188CE 802.11b/g/n WiFi Adapter", "REASON": "0 (No reason given)",
    "STATE": "100 (connected)", "TYPE": "802-11-wireless", "UDI":
    "/sys/devices/pci0000:00/0000:00:1c.1/0000:03:00.0/net/wlan0", "VENDOR":
    "Realtek Semiconductor Co., Ltd.", "state": "connected", "type":
    "802-11-wireless"}}, "running": "running", "state": "connected"}},
    "changed": true}

    PLAY RECAP ********************************************************************
    127.0.0.1                  : ok=2    changed=1    unreachable=0 failed=0


