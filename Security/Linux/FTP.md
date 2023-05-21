---
title: FTP
date: M01-28-2023
author: i4
---

## Description
Fast way to get an FTP server running.

## Installation
`apt update && apt install pure-ftpd -y`

```bash
cat <<EOF > setup-ft.sh 
	#!/bin/bash
	sudo groupadd ftpgroup
	sudo useradd -g ftpgroup -d /dev/null -s /etc ftpuser
	sudo pure-pw useradd i4 -u ftpuser -d /ftphome
	sudo pure-pw mkdb
	cd /etc/pure-ftpd/auth/
	sudo ln -s ../conf/PureDB 60pdb
	sudo mkdir -p /ftphome
	sudo chown -R ftpuser:ftpgroup /ftphome/
	sudo systemctl restart pure-ftpd
	EOF
```
