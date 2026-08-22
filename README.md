# Publicar Brote en Cloudflare

Esta carpeta se despliega tal cual. Publica el **prototipo**: la interfaz completa
con datos de demostración, sin backend. Sirve para tenerlo en una URL propia, en
tu teléfono, y para mostrarlo. Los datos no se guardan en ningún servidor.

Para el producto real con base de datos, login y precios en vivo, la guía está en
`../design_handoff_brote_budget/DEPLOY.md`.

```
brote-cloudflare/
├── public/index.html   ← la app entera, un archivo, sin dependencias externas
├── wrangler.toml       ← configuración; el backend está comentado abajo
├── package.json
└── .gitignore
```

---

## Paso a paso

### 1. Instalar wrangler

Necesitás Node 20 o más nuevo.

```bash
cd brote-cloudflare
npm install
```

### 2. Entrar a tu cuenta de Cloudflare

```bash
npx wrangler login
```

Se abre el navegador y pide autorizar. Si tenés varias cuentas:

```bash
npx wrangler whoami
```

### 3. Probarlo local

```bash
npm run dev
```

Abrí `http://localhost:8787`. Es exactamente lo que se va a publicar.

### 4. Publicar

```bash
npm run deploy
```

Al terminar imprime la URL:

```
https://brote.<tu-subdominio>.workers.dev
```

Ya está en línea. Cada `npm run deploy` posterior actualiza en segundos.

### 5. Dominio propio (opcional)

Si tenés un dominio en Cloudflare, en el panel: **Workers & Pages → brote →
Settings → Domains & Routes → Add custom domain**. El certificado lo emite
Cloudflare solo.

También podés declararlo en `wrangler.toml`:

```toml
routes = [
  { pattern = "brote.tudominio.com", custom_domain = true }
]
```

### 6. Ponerlo detrás de login (recomendado)

El prototipo queda público en esa URL. Para que solo entren vos y tu familia,
sin escribir una línea de código:

**Zero Trust → Access → Applications → Add an application → Self-hosted**

- Dominio: la URL del Worker
- Identity provider: **Google**
- Policy: *Allow* → *Emails* → los mails que correspondan

Cloudflare intercepta antes de que la request llegue al Worker: quien no esté en
la lista ve la pantalla de Google y nada más. Es gratis hasta 50 usuarios, y es el
mismo mecanismo que después usa el producto real.

---

## Actualizar el prototipo

`public/index.html` es un archivo compilado a partir del diseño. No lo edites a
mano: pedime los cambios en el diseño y te lo vuelvo a compilar acá.

---

## Cuánto sale

Nada. Un Worker con assets estáticos entra en el plan gratuito de sobra: el límite
es 100.000 requests por día.
