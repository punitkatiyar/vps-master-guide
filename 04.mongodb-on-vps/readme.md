

## Install MongoDB 9.0 Community Edition

### Step 1: Import MongoDB GPG key

```
curl -fsSL https://pgp.mongodb.com/server-9.asc | \
gpg --dearmor -o /usr/share/keyrings/mongodb-server-9.gpg
```

### Step 2: Add MongoDB repository

```
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/mongodb-server-9.gpg] https://repo.mongodb.org/apt/debian trixie/mongodb-org/9.0 main" \
| tee /etc/apt/sources.list.d/mongodb-org-9.0.list
```

### Step 3: Update package list

```
apt update
```

### Step 4: Install MongoDB

```
apt install -y mongodb-org
```

### Step 5: Start MongoDB

```
systemctl start mongod
```

### Step 6: Enable MongoDB on system boot

```
systemctl enable mongod
```
### Step 7: Check MongoDB status and Check installed versions
```
systemctl status mongod

mongod --version
mongosh --version
```

## Important MongoDB service commands

| Command |	Purpose |
|----|----|
| systemctl start mongod |	Start MongoDB |
| systemctl stop mongod |	Stop MongoDB |
| systemctl restart mongod | Restart MongoDB |
| systemctl status mongod |	Check status |
| systemctl enable mongod |	Start on boot |
| systemctl disable mongod	| Disable auto-start |
| journalctl -u mongod -f	 | View service logs |

## MongoDB default configuration:

| Configuration |	Location |
|----|----|
| Config file |	/etc/mongod.conf |
| Database files |	/var/lib/mongodb |
| Log file	| /var/log/mongodb/mongod.log |
| Default port |	27017 |









