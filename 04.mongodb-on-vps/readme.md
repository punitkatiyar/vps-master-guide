

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

