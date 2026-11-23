# Amigo Oculto 2026: como colocar no ar (Firebase grátis + GitHub)

Tudo no plano **Spark (grátis)**. O sorteio roda no **GitHub Actions** do próprio repo, com uma chave de servidor guardada como secret. Nenhum navegador recebe o resultado completo.

## 1. Firebase (console.firebase.google.com, projeto `amigoocultoprojeto`)
1. **Authentication → Começar → Método de login:** ativa **Google** e **E-mail/senha**.
2. **Authentication → Configurações → Domínios autorizados:** adiciona `gabrielmoura1998.github.io`.
3. **Firestore Database → Criar banco:** modo de produção, região `southamerica-east1`.
4. **Firestore → aba Regras:** apaga o que tiver lá, cola o conteúdo de `firestore.rules` e clica em **Publicar**.
5. **Firestore → Iniciar coleção** `config` com documento de ID `app` e estes campos:

   | campo | tipo | valor |
   |---|---|---|
   | `allowed` | array | e-mails dos 3 admins, **em minúsculas** |
   | `admins` | array | os mesmos 3 e-mails |
   | `finalLocked` | boolean | `false` |
   | `orderOpen` | boolean | `false` |
   | `testNo` | number | `0` |

6. **Engrenagem → Configurações do projeto → Seus apps → Web (`</>`):** registra o app, copia o `firebaseConfig` e cola no topo do `index.html`, no lugar dos `COLE_AQUI`.
7. **Engrenagem → Configurações do projeto → Contas de serviço → Gerar nova chave privada.** Vai baixar um `.json`. **Esse arquivo é a chave mestra do banco: nunca sobe ele no repo.**

## 2. GitHub
1. Cria o repo **público** `amigo-oculto`. Se usar outro nome, ajusta o `ACTIONS_URL` no topo do `index.html`.
2. **Add file → Upload files:** sobe o `index.html` e a pasta `scripts` (com `sorteio.js` e `package.json`).
3. **Add file → Create new file:** no nome, digita exatamente `.github/workflows/sorteio.yml` e cola o conteúdo do arquivo `sorteio.yml`. O upload pelo navegador às vezes ignora pastas que começam com ponto, por isso esse arquivo vai à mão.
4. **Settings → Secrets and variables → Actions → New repository secret:**
   - Name: `FIREBASE_SA`
   - Secret: abre o `.json` da chave do passo 1.7 e cola **o conteúdo inteiro**.
5. **Settings → Pages:** Branch `main`, pasta `/ (root)` → Save. Em 1–2 min o site está em `https://gabrielmoura1998.github.io/amigo-oculto/`.

## 3. Como sortear
**Actions → Sorteio → Run workflow** e escolhe:
- `teste`: corrente pública com nomes, sem intimidade. Roda quantas vezes quiser.
- `oficial`: digita `OFICIAL` na confirmação. Roda uma vez só e tranca tudo.
- `liberar-numeros`: no dia da festa, mostra "tu és o nº X" pra cada um.

O resultado aparece no site sozinho em cerca de 1 minuto. Os outros admins só conseguem rodar o workflow se forem colaboradores do repo (**Settings → Collaborators**).

## Cuidados
- **Os logs do Actions em repo público são públicos.** Por isso o script do oficial mostra só as checagens, nunca nomes nem códigos.
- Quem tem a chave de servidor ou abre a coleção `results` no console consegue ver quem tirou quem. Isso vale pra qualquer backend: é questão de confiança em quem administra.
- Depois da festa, apaga o secret `FIREBASE_SA` e revoga a chave em **Contas de serviço** no Google Cloud.

## Coleções
- `config/app`: liberados, admins, trava do oficial
- `people/{uid}`: nome e se está pronto (todos veem)
- `users/{uid}`: perfil e gostos (dono, admins e quem tirou a pessoa)
- `intim/{uid}`: intimidade (só o dono)
- `tests/latest`: último teste (todos veem)
- `results/{uid}`, `positions/{uid}`: resultado e posição (só o dono)
- `audit/final`: auditoria por códigos (só admins)
- `private/order`: ordem da corrente (ninguém lê pelo site)
