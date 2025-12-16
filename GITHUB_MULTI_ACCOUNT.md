# 🔐 GitHub 다중 계정 관리 (Multiple Accounts)

## 문제

계정이 2개 이상일 때 `git config --global credential.helper store`를 사용하면:
- ❌ 계정 충돌 발생
- ❌ 잘못된 계정으로 push
- ❌ 인증 에러

## ✅ 해결 방법 (3가지)

---

## 방법 1: SSH Keys (가장 권장!) ⭐

**장점:**
- ✅ 각 계정별 SSH key 사용
- ✅ 자동 인증
- ✅ Token 관리 불필요
- ✅ 가장 안전

### Setup:

#### 1) SSH Key 생성 (계정별)

```bash
# 계정1 SSH key
ssh-keygen -t ed25519 -C "account1@email.com" -f ~/.ssh/id_ed25519_account1

# 계정2 SSH key (juyoungahn용)
ssh-keygen -t ed25519 -C "jy93630@naver.com" -f ~/.ssh/id_ed25519_juyoungahn
```

#### 2) SSH Config 설정

```bash
# SSH config 파일 생성/편집
cat > ~/.ssh/config << 'EOF'
# 계정1 (기본 또는 다른 계정)
Host github.com-account1
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_account1

# 계정2 (juyoungahn)
Host github.com-juyoungahn
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_juyoungahn
EOF

# 권한 설정
chmod 600 ~/.ssh/config
```

#### 3) GitHub에 Public Key 등록

```bash
# 계정2 (juyoungahn) public key 복사
cat ~/.ssh/id_ed25519_juyoungahn.pub
```

**GitHub에서:**
1. https://github.com/settings/keys 로그인 (juyoungahn 계정)
2. **New SSH key** 클릭
3. Title: `My Server - juyoungahn`
4. Key: [복사한 내용 붙여넣기]
5. **Add SSH key**

#### 4) Remote URL을 SSH로 변경

```bash
cd /root/LangChain-for-LLM-Application-Development

# juyoungahn 계정용 SSH URL
git remote set-url myfork git@github.com-juyoungahn:JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git

# 확인
git remote -v
```

#### 5) Push (자동 인증!)

```bash
git push myfork main
# Username/Password 입력 없이 바로 됨!
```

---

## 방법 2: Repository별 Credential 설정

**장점:**
- ✅ Repository마다 다른 인증 정보
- ✅ Token 기반
- ⚠️ Token 관리 필요

### Setup:

#### 1) Token 생성

각 계정별로 Token 생성:
- 계정1: https://github.com/settings/tokens (계정1로 로그인)
- 계정2 (juyoungahn): https://github.com/settings/tokens (juyoungahn으로 로그인)

#### 2) Remote URL에 Token 포함

```bash
cd /root/LangChain-for-LLM-Application-Development

# juyoungahn 계정 + Token
git remote set-url myfork https://juyoungahn:ghp_YOUR_TOKEN@github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git

# Push (자동 인증)
git push myfork main
```

⚠️ **주의:** Token이 URL에 노출되므로 조심!

#### 3) 또는 Local Credential Helper 사용

```bash
# 이 repository만 credential helper 사용
cd /root/LangChain-for-LLM-Application-Development
git config credential.helper store  # global 없이!

# Push (Token 입력)
git push myfork main
Username: juyoungahn
Password: ghp_YOUR_TOKEN

# Credential은 .git/config에만 저장됨
```

---

## 방법 3: Git Credential Manager (고급)

**장점:**
- ✅ 여러 계정 자동 관리
- ✅ GUI로 계정 선택
- ✅ Token 자동 갱신

### Setup:

```bash
# Git Credential Manager 설치
curl -LO https://github.com/git-ecosystem/git-credential-manager/releases/latest/download/gcm-linux_amd64.2.0.935.tar.gz
tar -xvf gcm-linux_amd64.2.0.935.tar.gz
sudo mv git-credential-manager /usr/local/bin/

# 설정
git config --global credential.credentialStore secretservice
git config --global credential.helper manager
```

---

## 🎯 권장 설정 (당신의 경우)

### 계정 정보:
- **계정1**: (기본 계정 - 이름?)
- **계정2**: juyoungahn (LangChain repo용)

### 추천: SSH 방식 ⭐

```bash
# 1. SSH Key 생성
ssh-keygen -t ed25519 -C "juyoungahn@email.com" -f ~/.ssh/id_ed25519_juyoungahn
# Enter 3번 (passphrase 없이)

# 2. SSH Config
cat >> ~/.ssh/config << 'EOF'

# juyoungahn 계정
Host github.com-juyoungahn
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_juyoungahn
EOF

chmod 600 ~/.ssh/config

# 3. Public Key 복사
cat ~/.ssh/id_ed25519_juyoungahn.pub
# 출력된 내용 복사

# 4. GitHub에 등록
echo "👉 https://github.com/settings/keys 에서 추가"
echo "   Title: My Server - juyoungahn"
echo "   Key: [위에서 복사한 내용]"

# 5. Remote URL 변경
cd /root/LangChain-for-LLM-Application-Development
git remote set-url myfork git@github.com-juyoungahn:JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git

# 6. 테스트
ssh -T git@github.com-juyoungahn
# 출력: Hi JuYoungAhn! You've successfully authenticated...

# 7. Push!
git push myfork main
# 자동으로 됨!
```

---

## 📊 방법 비교

| 방법 | 보안 | 편의성 | 설정 복잡도 | 추천도 |
|------|------|--------|------------|--------|
| **SSH Keys** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ✅ **강력추천** |
| Repository별 Token | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ✅ 괜찮음 |
| URL에 Token 포함 | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ | ⚠️ 비추천 |
| Git Credential Manager | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅ 좋음 (고급) |

---

## 🚨 피해야 할 것

### ❌ 하지 말 것:

```bash
# ❌ Global credential helper (계정 충돌!)
git config --global credential.helper store

# ❌ 같은 Token 여러 계정에 사용
# (각 계정별로 Token 생성해야 함)

# ❌ Token을 코드에 포함
git remote set-url myfork https://ghp_TOKEN@github.com/...
git add .  # ← 이러면 Token이 노출될 수 있음!
```

---

## 🎯 빠른 실행 스크립트

### SSH 방식 (추천)

```bash
#!/bin/bash

echo "🔐 Setting up SSH for juyoungahn account"

# 1. Generate SSH key
echo "1️⃣ Generating SSH key..."
ssh-keygen -t ed25519 -C "juyoungahn@email.com" -f ~/.ssh/id_ed25519_juyoungahn -N ""

# 2. Create SSH config
echo "2️⃣ Configuring SSH..."
cat >> ~/.ssh/config << 'EOF'

# juyoungahn GitHub account
Host github.com-juyoungahn
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_juyoungahn
EOF

chmod 600 ~/.ssh/config

# 3. Show public key
echo "3️⃣ Your public key (copy this):"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
cat ~/.ssh/id_ed25519_juyoungahn.pub
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

echo ""
echo "4️⃣ Next steps:"
echo "   a. Go to: https://github.com/settings/keys"
echo "   b. Click 'New SSH key'"
echo "   c. Title: 'My Server - juyoungahn'"
echo "   d. Paste the key above"
echo ""
echo "5️⃣ Then update your remote:"
echo "   cd /root/LangChain-for-LLM-Application-Development"
echo "   git remote set-url myfork git@github.com-juyoungahn:JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git"
echo ""
echo "6️⃣ Test connection:"
echo "   ssh -T git@github.com-juyoungahn"
echo ""
echo "7️⃣ Push:"
echo "   git push myfork main"
```

Save as `setup_ssh.sh` and run:
```bash
chmod +x setup_ssh.sh
./setup_ssh.sh
```

---

## ✅ 확인 방법

### SSH 연결 테스트:

```bash
# juyoungahn 계정 테스트
ssh -T git@github.com-juyoungahn

# 성공 시 출력:
# Hi JuYoungAhn! You've successfully authenticated, but GitHub does not provide shell access.
```

### Remote 확인:

```bash
git remote -v

# SSH 방식 (권장):
# myfork  git@github.com-juyoungahn:JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git (fetch)
# myfork  git@github.com-juyoungahn:JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git (push)

# HTTPS 방식:
# myfork  https://github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git (fetch)
# myfork  https://github.com/JuYoungAhn/LangChain-for-LLM-Application-Development-2025.git (push)
```

---

## 🔧 Troubleshooting

### 문제: "Permission denied (publickey)"

```bash
# SSH key가 제대로 등록되었는지 확인
ssh -T git@github.com-juyoungahn

# SSH agent 시작
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_juyoungahn

# 다시 시도
ssh -T git@github.com-juyoungahn
```

### 문제: "Could not resolve hostname"

```bash
# SSH config 확인
cat ~/.ssh/config

# Host 이름 확인
# Remote URL과 SSH config의 Host가 일치해야 함
git remote -v
```

---

## 📝 요약

### 당신에게 최적의 방법:

**SSH Keys 방식** ⭐⭐⭐⭐⭐

```bash
# 5분 설정으로 평생 편하게!
1. SSH key 생성
2. GitHub에 등록
3. SSH config 설정
4. Remote URL 변경
5. 완료! 자동 인증
```

**장점:**
- 계정별 관리 쉬움
- Token 관리 불필요
- 가장 안전
- 자동 인증

이 방법 사용하세요! 🚀
