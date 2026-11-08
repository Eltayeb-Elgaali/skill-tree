```bash
mkdir -p /root/miniapp
cat > /root/miniapp/app.py <<'EOF'
from greeting import greet

def main():
    print(greet("world"))

if __name__ == "__main__":
    main()
EOF
cat > /root/miniapp/greeting.py <<'EOF'
def greet(name):
    return f"Hello, {name}!"
EOF
```

```bash
cd /root/miniapp && claude
```

```bash
Read this project and write OVERVIEW.md describing what it does, how the files connect, and how to run it.
```
```bash
cat /root/miniapp/OVERVIEW.md
```