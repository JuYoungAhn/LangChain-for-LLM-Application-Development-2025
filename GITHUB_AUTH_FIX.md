# 🔐 GitHub Authentication Fix

## Problem

```
remote: Invalid username or token. Password authentication is not supported for Git operations.
```

GitHub는 2021년 8월부터 password 인증을 지원하지 않습니다. **Personal Access Token (PAT)**을 사용해야 합니다.

---

## ✅ Solution: Create Personal Access Token

### Step 1: Create Token on GitHub

1. **GitHub에 로그인**
   - Go to: https://github.com

2. **Settings로 이동**
   - 우측 상단 프로필 클릭 → **Settings**

3. **Developer settings**
   - 왼쪽 메뉴 맨 아래 → **Developer settings**

4. **Personal access tokens**
   - **Personal access tokens** → **Tokens (classic)**
   - 또는 **Fine-grained tokens** (더 안전)

5. **Generate new token**
   - **Generate new token (classic)** 클릭

6. **Token 설정**
   ```
   Note: LangChain Repository Push (2024)
   Expiration: 90 days (또는 원하는 기간)
   
   Scopes (권한):
   ✅ repo (전체 체크)
      ✅ repo:status
      ✅ repo_deployment
      ✅ public_repo
      ✅ repo:invite
      ✅ security_events
   ```

7. **Generate token**
   - 맨 아래 **Generate token** 클릭

8. **Token 복사** ⚠️ **중요!**
   ```
   ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```
   - ⚠️ **지금 바로 복사하세요!** 다시 볼 수 없습니다.
   - 안전한 곳에 저장 (예: 1Password, LastPass)

---

## 🚀 Step 2: Push with Token

### Method 1: Token을 Password로 사용 (간단)

```bash
git push myfork main

# Username 입력
Username: juyoungahn

# Password 대신 Token 입력
Password: ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Method 2: URL에 Token 포함 (자동화)

```bash
# Remote URL 변경 (Token 포함)
git remote set-url myfork https://ghp_YOUR_TOKEN@github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git

# Push (인증 불필요)
git push myfork main
```

⚠️ **주의:** Token이 URL에 포함되므로 조심하세요!

### Method 3: Git Credential Helper 사용 (권장)

```bash
# Credential helper 설정
git config --global credential.helper store

# 첫 번째 push (Token 입력)
git push myfork main
Username: juyoungahn
Password: ghp_YOUR_TOKEN

# 이후부터는 자동으로 인증됨
```

Token은 `~/.git-credentials`에 저장됩니다.

---

## 📋 Quick Fix Commands

### Option A: 즉시 Push (Token 사용)

```bash
# 1. Token 준비
# GitHub에서 생성한 token을 복사

# 2. Push
git push myfork main

# 3. Username/Password 입력
Username: juyoungahn
Password: [여기에 Token 붙여넣기]
```

### Option B: Credential Store 설정 후 Push

```bash
# 1. Credential helper 설정
git config --global credential.helper store

# 2. Push
git push myfork main

# 3. 한 번만 Token 입력
Username: juyoungahn
Password: [Token 붙여넣기]

# 4. 이후 자동 인증됨
```

### Option C: SSH 사용 (가장 안전)

```bash
# 1. SSH key 생성 (없는 경우)
ssh-keygen -t ed25519 -C "your_email@example.com"
# Enter 3번 (기본 설정)

# 2. Public key 복사
cat ~/.ssh/id_ed25519.pub

# 3. GitHub에 추가
# Settings → SSH and GPG keys → New SSH key
# Title: My Server
# Key: [복사한 내용 붙여넣기]

# 4. Remote URL을 SSH로 변경
git remote set-url myfork git@github.com:JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git

# 5. Push (인증 자동)
git push myfork main
```

---

## 🎯 Recommended: Complete Setup Script

```bash
#!/bin/bash

echo "🔐 GitHub Authentication Setup"
echo "=============================="

# Check if token is provided
if [ -z "$1" ]; then
    echo "Usage: ./setup_auth.sh YOUR_GITHUB_TOKEN"
    echo ""
    echo "Get token from: https://github.com/settings/tokens"
    exit 1
fi

TOKEN=$1

# Configure git
echo "📝 Configuring git..."
git config --global credential.helper store
git config --global user.name "juyoungahn"
git config --global user.email "your_email@example.com"  # Update this

# Update remote URL with token
echo "🔗 Updating remote URL..."
git remote set-url myfork https://${TOKEN}@github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git

# Test connection
echo "🧪 Testing connection..."
git ls-remote myfork

if [ $? -eq 0 ]; then
    echo "✅ Authentication successful!"
    echo ""
    echo "You can now push:"
    echo "  git push myfork main"
else
    echo "❌ Authentication failed. Please check your token."
fi
```

Save as `setup_auth.sh` and run:
```bash
chmod +x setup_auth.sh
./setup_auth.sh ghp_YOUR_TOKEN_HERE
```

---

## 🔒 Security Best Practices

### DO ✅
- Token을 안전하게 저장 (password manager)
- Token에 필요한 최소 권한만 부여
- 만료 기간 설정
- 사용하지 않는 token은 삭제

### DON'T ❌
- Token을 코드에 포함하지 말 것
- Public repository에 token 올리지 말 것
- Token을 평문으로 공유하지 말 것
- 너무 긴 만료 기간 설정하지 말 것

---

## 🆘 Troubleshooting

### 문제: "Authentication failed"

**해결:**
1. Token이 올바른지 확인
2. Token 권한 확인 (repo 체크되어 있는지)
3. Token이 만료되지 않았는지 확인
4. Username이 정확한지 확인

### 문제: "Could not read from remote repository"

**해결:**
```bash
# Repository 존재 확인
# https://github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025

# Remote URL 확인
git remote -v

# Remote URL 수정 (필요시)
git remote set-url myfork https://github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git
```

### 문제: Token을 잃어버렸을 때

**해결:**
1. GitHub → Settings → Developer settings
2. 기존 token 삭제
3. 새 token 생성
4. 새 token으로 다시 인증

---

## 📱 Quick Reference

### Token 생성 URL
```
https://github.com/settings/tokens/new
```

### Token 권한 (Scopes)
```
✅ repo (full control of private repositories)
```

### Push with Token
```bash
git push myfork main
Username: juyoungahn
Password: ghp_YOUR_TOKEN
```

### Store Credentials
```bash
git config --global credential.helper store
```

---

## ✅ Final Check

After setup, verify:

```bash
# 1. Check remote
git remote -v

# 2. Check credentials (if using credential helper)
cat ~/.git-credentials

# 3. Test connection
git ls-remote myfork

# 4. Push
git push myfork main
```

---

**이제 push가 가능합니다! 🚀**

Token 생성 후 위의 방법 중 하나를 선택하여 사용하세요.
