# 🪐 Planeta Sinay RPG — Ficha do Siny (VTT)

Aplicação web de página única (SPA) para gerenciar fichas de **Siny** no RPG de mesa **Planeta Sinay**, com estética cartoon/HQ, sincronização em tempo real e painel do narrador.

Tudo vive em um único arquivo: **`index.html`**. Sem build, sem `npm install`.

---

## ▶️ Como rodar

### Modo Local (imediato, sem configurar nada)
Basta **abrir `index.html`** no navegador (duplo clique). O app roda em **Modo Local**: os dados ficam salvos no `localStorage` deste navegador. Ótimo pra testar e jogar offline.

> No modo local não há sincronização entre dispositivos, e a busca/clonagem por ID e a lista completa do narrador ficam limitadas a este navegador.

### Modo Online / Tempo Real (com Firebase)
Para sincronizar a mesa entre vários jogadores e habilitar o painel do narrador completo:

1. Crie um projeto no [Firebase Console](https://console.firebase.google.com/).
2. Ative **Authentication → Sign-in method → Anonymous**.
3. Crie um **Cloud Firestore** (modo produção).
4. Em **Project settings → Your apps → Web app**, copie o objeto de config.
5. Cole no topo do `<script type="module">` em `index.html`, substituindo `USER_FIREBASE_CONFIG`:

```js
const USER_FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "seu-projeto.firebaseapp.com",
  projectId: "seu-projeto",
  storageBucket: "seu-projeto.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abc123",
};
```

6. **Sirva por HTTP(S)** (o login anônimo do Firebase não funciona bem em `file://`):
   - `npx serve .`  ou  `python -m http.server 8080`
   - ou publique em qualquer host estático (Netlify, Vercel, GitHub Pages, Firebase Hosting).

#### Regras do Firestore
As fichas ficam na rota pública `artifacts/{appId}/public/data/sheets/{userId}`. Regras mínimas:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /artifacts/{appId}/public/data/sheets/{sheetId} {
      allow read, write: if request.auth != null;   // exige login (anônimo serve)
    }
  }
}
```

> Para uma mesa fechada, troque por regras mais restritas (ex.: só o dono escreve a própria ficha, e o narrador via custom claim). As regras acima são o suficiente para jogar entre amigos.

---

## 🎮 Funcionalidades

- **5 Tipos Psicológicos** (Blindado, Obcecado, Desgastado, Explosivo, Sonhador) que redefinem Vida, Sanidade e os pools de pontos.
- **Atributos vitais**: Vida ❤️ e Sanidade 🧠 em destaque (anéis), + Agilidade, Blefe, Poder, Furtividade.
- **Pool de Atributos e de Treino** com trava automática (libere com o **Modo Livre / Mestre**).
- **8 Perícias** com 4 níveis cada, sincronizadas ao pool de treino.
- **Medidor de Infecção por Gosma** (0–100%) que muda de verde a vermelho.
- **Mutação do Monstro**: quando Sanidade ≤ 0 **ou** Infecção ≥ 100%, a ficha entra em corrupção (tremor, distorção de cor, overlay "Siny Corrompido!", troca pro avatar corrompido). Botão **Purificar Siny** restaura.
- **Duplo avatar** (saudável/corrompido): upload local (Base64) ou templates vetoriais (🌸 Flor, ☁️ Nuvem, 🟢 Gosma).
- **Compartilhar Siny**: gera link de espectador + mensagem pronta pro WhatsApp.
- **Modo Espectador** via `?siny={userId}` (somente leitura, com banner amarelo) e botão **Clonar Ficha**.
- **Mesa do Narrador** (senha **`inay1234`**): lista os Sinies em tempo real e aplica Dano (−10 ❤️), Pânico (−10 🧠), Contaminação (+10% ☣️), abre a ficha pra edição direta e deleta fichas.
- **Backup Base64** para salvar/carregar a ficha como texto.

---

## 🔐 Senha do Narrador
`inay1234` (definida em `GM_PASSWORD` no `index.html`).

## 🧱 Stack
HTML5 · Tailwind (CDN) · JavaScript ES6+ (módulos) · Firebase Auth (anônimo) + Cloud Firestore · ícones Lucide · fonte Fredoka.
