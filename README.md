# CPE Tecnologia — Convenção de Vendas 2026
App de votação colaborativa em tempo real para a Convenção de Vendas CPE.

## Tecnologias
- **Frontend:** HTML/CSS/JS puro (zero dependências além do Firebase SDK)
- **Backend:** Firebase Realtime Database (sincronização em tempo real)
- **Hosting:** GitHub Pages (gratuito)

---

## PASSO 1 — Criar o projeto no Firebase

1. Acesse [console.firebase.google.com](https://console.firebase.google.com)
2. Clique em **"Adicionar projeto"**
3. Nome sugerido: `cpe-convencao-2026`
4. Desative o Google Analytics (não é necessário)
5. Clique em **"Criar projeto"**

### 1.1 — Criar o Realtime Database

1. No menu lateral: **Build → Realtime Database**
2. Clique em **"Criar banco de dados"**
3. Escolha a região: **us-central1** (ou a mais próxima)
4. Selecione **"Iniciar no modo de teste"** (você vai proteger depois)
5. Clique em **"Ativar"**

### 1.2 — Configurar Regras de Segurança

No Realtime Database, clique na aba **"Regras"** e cole:

```json
{
  "rules": {
    "cpe_conv_2026": {
      ".read": true,
      ".write": true
    }
  }
}
```

> ⚠️ Estas regras permitem leitura/escrita pública — adequado para uso interno
> em um evento fechado. Após a convenção, troque `.write` para `false`.

### 1.3 — Obter as credenciais

1. No menu lateral: **Configurações do projeto (⚙️) → Configurações gerais**
2. Role até **"Seus apps"** e clique em **"Adicionar app → Web (</>)"**
3. Apelido: `cpe-convencao-web`
4. **Não** marque "Firebase Hosting"
5. Clique em **"Registrar app"**
6. Copie o bloco `firebaseConfig` que aparece:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "cpe-convencao-2026.firebaseapp.com",
  databaseURL: "https://cpe-convencao-2026-default-rtdb.firebaseio.com",
  projectId: "cpe-convencao-2026",
  storageBucket: "cpe-convencao-2026.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## PASSO 2 — Configurar o arquivo index.html

Abra o arquivo `index.html` e localize o bloco marcado com `// ① CONFIGURAÇÃO FIREBASE`:

```js
const FIREBASE_CONFIG = {
  apiKey:            "COLE_AQUI_SUA_API_KEY",
  authDomain:        "COLE_AQUI.firebaseapp.com",
  databaseURL:       "https://COLE_AQUI-default-rtdb.firebaseio.com",
  projectId:         "COLE_AQUI",
  storageBucket:     "COLE_AQUI.appspot.com",
  messagingSenderId: "COLE_AQUI",
  appId:             "COLE_AQUI"
};
```

Substitua cada `"COLE_AQUI..."` com os valores copiados do Firebase Console.

---

## PASSO 3 — Publicar no GitHub Pages

### 3.1 — Criar o repositório

1. Acesse [github.com](https://github.com) e faça login
2. Clique em **"New repository"**
3. Nome sugerido: `cpe-convencao-2026`
4. Deixe como **Public**
5. Clique em **"Create repository"**

### 3.2 — Enviar o arquivo

**Opção A — pelo navegador (mais simples):**
1. Na página do repositório, clique em **"uploading an existing file"**
2. Arraste o `index.html` para a área
3. Clique em **"Commit changes"**

**Opção B — via Git:**
```bash
git init
git add index.html
git commit -m "CPE Convenção 2026 - versão inicial"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/cpe-convencao-2026.git
git push -u origin main
```

### 3.3 — Ativar o GitHub Pages

1. No repositório, vá em **Settings → Pages**
2. Em **"Source"**, selecione: `Deploy from a branch`
3. Branch: **main** / Folder: **/ (root)**
4. Clique em **"Save"**
5. Aguarde ~2 minutos. A URL será:
   ```
   https://SEU_USUARIO.github.io/cpe-convencao-2026/
   ```

### 3.4 — Autorizar o domínio no Firebase

Para que o Firebase aceite requisições do GitHub Pages:

1. No Firebase Console: **Configurações do projeto → Domínios autorizados**
2. Clique em **"Adicionar domínio"**
3. Cole: `SEU_USUARIO.github.io`
4. Salve

---

## PASSO 4 — Testar antes da convenção

1. Abra a URL do GitHub Pages em dois navegadores/abas
2. Em uma aba, entre como **Facilitador** → crie grupos e uma ação
3. Na outra aba, entre como **Participante** → deve ver a ação em segundos
4. Confirme que a sincronização está funcionando

---

## Fluxo de uso no evento

```
ANTES        Facilitador cria grupos e associa participantes
             Facilitador configura pesos dos critérios

DURANTE      Grupos propõem ações (aba "Cadastrar")
             Facilitador aprova ou rejeita ações duplicadas
             Facilitador abre a votação
             Participantes votam nas ações de outros grupos

APÓS         Facilitador fecha a votação
             Todos acessam o Ranking Final ao vivo
```

---

## Solução de problemas

| Sintoma | Causa provável | Solução |
|---|---|---|
| Overlay de loading não some | Firebase não configurado | Verifique as credenciais no `FIREBASE_CONFIG` |
| "Permission denied" no console | Regras do DB muito restritivas | Verifique as regras no Firebase Console |
| App funciona mas não sincroniza | `databaseURL` incorreto | Confira a URL no Firebase Console → Realtime Database |
| GitHub Pages mostra 404 | Pages não ativado ou branch errado | Settings → Pages → Source: main / root |
| Domínio bloqueado | GitHub Pages não autorizado | Firebase → Domínios autorizados → adicionar `*.github.io` |

---

## Após a convenção

1. No Firebase, altere as regras para bloquear escrita:
```json
{
  "rules": {
    "cpe_conv_2026": {
      ".read": true,
      ".write": false
    }
  }
}
```
2. Exporte os dados: Firebase Console → Realtime Database → ⋮ → **Exportar JSON**

