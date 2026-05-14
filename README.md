# Portfolio 03 — Cypress Mid | E2E Testing with Network Control

> End-to-end test suite for [Saucedemo](https://www.saucedemo.com) built with **Cypress + JavaScript**, demonstrating network interception, custom commands, and edge case coverage.

---

## 📁 Project Structure

```
portfolio-03-cypress-mid/
├── cypress/
│   ├── e2e/
│   │   ├── login.cy.js        # Login smoke + data-driven tests
│   │   └── inventory.cy.js    # Inventory tests + network interception
│   ├── fixtures/              # Static test data (JSON)
│   └── support/
│       ├── commands.js        # Custom cy.login() command
│       └── e2e.js             # Global support file
├── cypress.config.js          # Cypress configuration
├── package.json
└── README.md
```

---

## ✅ Prerequisites

- [Node.js](https://nodejs.org/) v18 or higher
- npm v9 or higher

---

## 🚀 Getting Started

**1. Clone the repository:**

```bash
git clone https://github.com/TU_USUARIO/portfolio-03-cypress-mid.git
cd portfolio-03-cypress-mid
```

**2. Install dependencies:**

```bash
npm install
```

**3. Run tests headless:**

```bash
npx cypress run
```

**4. Open interactive UI:**

```bash
npx cypress open
```

---

## 🧪 Test Coverage

### `login.cy.js` — Login Suite

| Test | Type | Description |
|------|------|-------------|
| Login with valid user | Smoke | Asserts redirect to `/inventory` after successful login |
| Login with locked user | Negative | Asserts error message contains `"locked out"` |
| Login with invalid credentials | Negative | Asserts error message is visible |
| Empty fields | Data-driven | Asserts validation error |
| Missing password | Data-driven | Asserts validation error |
| Missing username | Data-driven | Asserts validation error |
| Non-existent user | Data-driven | Asserts validation error |

### `inventory.cy.js` — Inventory + Network Suite

| Test | Type | Description |
|------|------|-------------|
| Shows 6 products | Smoke | Asserts inventory renders all items |
| Add item updates cart badge | Smoke | Asserts badge shows `"1"` after adding first item |
| Go to cart after adding item | Smoke | Asserts redirect to `/cart` |
| Intercept — simulated empty response | Network | Intercepts GET, returns empty body, asserts 0 items rendered |
| Intercept — 500 server error | Network | Intercepts GET, returns 500, asserts inventory list does not exist |
| Intercept — slow response (3s delay) | Network | Intercepts GET, adds 3000ms delay, tests loading behavior |

---

## 🌐 Network Interception — Key Feature

This project demonstrates Cypress's `cy.intercept()` to control network behavior without modifying the backend:

**Happy path (200):**
```javascript
cy.intercept('GET', '**/inventory*', { statusCode: 200, body: { items: [] } }).as('getInventory')
cy.wait('@getInventory')
```

**Server error (500):**
```javascript
cy.intercept('GET', '**/inventory*', { statusCode: 500, body: { error: 'Internal Server Error' } }).as('serverError')
cy.wait('@serverError')
```

**Slow response:**
```javascript
cy.intercept('GET', '**/inventory*', (req) => {
  req.reply((res) => { res.setDelay(3000) })
}).as('slowResponse')
cy.wait('@slowResponse')
```

---

## 🛠️ Custom Commands

Defined in `cypress/support/commands.js`:

```javascript
cy.login(username, password)
```

Visits `/`, fills credentials, and clicks login. Defaults to `standard_user` / `secret_sauce` if no arguments are passed. Used in `beforeEach` blocks to keep specs clean and DRY.

---

## ⚙️ Configuration

`cypress.config.js` highlights:

| Option | Value | Why |
|--------|-------|-----|
| `baseUrl` | `https://www.saucedemo.com` | Single source of truth for the app URL |
| `defaultCommandTimeout` | `8000ms` | Handles occasional slow responses from the demo app |
| `viewportWidth/Height` | `1280x720` | Standard HD desktop viewport |
| `video` | `false` | Disabled to reduce artifact size; screenshots on failure are enough |
| `screenshotOnRunFailure` | `true` | Auto-captures UI state when a test fails |

---

## 📊 Viewing Test Results

After running `npx cypress run`, screenshots of any failures are saved to:

```
cypress/screenshots/
```

To open the interactive test runner and watch tests execute in real time:

```bash
npx cypress open
```

---

## 🧠 Key Concepts Demonstrated

- **Custom commands** — reusable `cy.login()` keeps specs short and readable
- **Network interception** — `cy.intercept` stubs API responses without touching the backend
- **Data-driven tests** — `forEach` loop over user datasets avoids test duplication
- **Negative testing** — locked users, invalid credentials, empty fields
- **Async control** — `cy.wait('@alias')` ensures intercepts fire before assertions

---

## 📌 App Under Test

[Saucedemo](https://www.saucedemo.com) — a public demo e-commerce app by Sauce Labs, designed specifically for practicing test automation.

**Test users available:**

| Username | Behavior |
|----------|----------|
| `standard_user` | Normal login |
| `locked_out_user` | Login blocked |
| `problem_user` | UI bugs intentionally present |
| `performance_glitch_user` | Slow login response |

Password for all users: `secret_sauce`
