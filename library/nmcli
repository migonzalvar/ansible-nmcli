#!/usr/bin/env python
# -*- encoding: utf-8 -*-

# Copyright (C) 2013 Miguel González
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.


import json
import shlex
import subprocess
import sys


DOCUMENTATION = '''
---
module: nmcli
short_description: Gather networkmanager information using nmcli
description:
    - Execute nmcli command to gather information about network manager
      devices. The information is available as ansible_facts.
author: Miguel González
'''


EXAMPLES = '''
# Get local devices
- hosts: 127.0.0.1
  connection: local
  tasks:
    - name: get custom facts
      action: nmcli
'''


NMCLI_FIELDS = {
    'nm': "RUNNING STATE WIFI-HARDWARE WIFI WWAN-HARDWARE WWAN".split(),
    'con': "NAME UUID TYPE TIMESTAMP-REAL".split(),
    'dev': "DEVICE TYPE STATE".split(),
}


def _shell(args):
    """Execute args and returns status code, stdout and stderr"""
    try:
        process = subprocess.Popen(args, stdout=subprocess.PIPE,
                                   stderr=subprocess.PIPE)
        stdout, stderr = process.communicate()
        retcode = process.returncode
    except Exception as e:
        cmd = " ".join(args)
        msg = "{0} when executing command '{1}'".format(repr(e), cmd)
        fail_json(msg)

    return retcode, stdout, stderr


def _nmcli(obj, command=None, fields=None, multiline=False):
    """Wraps nmcli execution"""
    if fields is None:
        fields = NMCLI_FIELDS[obj]

    args = ['nmcli', '--terse', '--fields', ",".join(fields), obj]
    if command:
        args += shlex.split(command)
    retcode, stdout, stderr = _shell(args)

    data = []
    if retcode == 0:
        if multiline:
            # prev_field = None
            row = {}
            for line in stdout.split('\n'):
                values = line.split(':', 1)
                if len(values) == 2:
                    multikey, value = values
                    field, prop = multikey.split('.')
                    row[prop] = value
            data.append(row)
        else:
            for line in stdout.split('\n'):
                values = line.split(':')
                if len(values) == len(fields):
                    row = dict(zip(fields, values))
                    data.append(row)
        return data
    else:
        msg = "nmcli return {0} code. STDERR='{1}'".format(retcode, stderr)
        fail_json(msg)


def get_nm_status():
    """Execute and parse nmcli nm status"""
    data = _nmcli('nm', 'status', ['running', 'state'])
    return data[0]


def get_nm_devices():
    """Execute and parse nmcli dev status"""
    devices = {}
    fields = ['device', 'type', 'state']
    data = _nmcli('dev', 'status', fields)
    for row in data:
        devices[row['device']] = {'type': row['type'], 'state': row['state']}
    return devices


def get_nm_dev_details(iface):
    """Execute nmcli dev list iface"""
    data = _nmcli('dev', 'list iface {0}'.format(iface), ['general'],
                 multiline=True)
    return data[0]


def main(**kwargs):
    nm = get_nm_status()

    devices = get_nm_devices()
    for iface in devices:
        details = get_nm_dev_details(iface)
        devices[iface].update(details)
    nm['devices'] = devices

    exit_json(ansible_facts={'ansible_networkmanager': nm})


def exit_json(changed=True, **kwargs):
    result = {'changed': changed}
    result.update(kwargs)
    print json.dumps(result, sort_keys=False, indent=2)
    sys.exit(0)


def fail_json(msg):
    print json.dumps({"failed": True, "msg": msg}, indent=2)
    sys.exit(1)


if __name__ == '__main__':
    try:
        args_file = sys.argv[1]
    except IndexError:
        fail_json('No argument file')

    args_data = file(args_file).read()
    arguments = shlex.split(args_data)
    kwargs = dict([arg.split("=") for arg in arguments if "=" in arg])

    main(**kwargs)
