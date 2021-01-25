# Contributing

## Setup local dev environment

```shell
git clone git@github.com:daritelska-platforma/frontend.git
cd frontend

# Symlink dev environment
ln -s .env.example .env

# Install dependencies
yarn
```

## Development

```shell
yarn dev
```

Visit <http://localhost:3040/>

## Run via Docker Compose

Install the binary via <https://docs.docker.com/compose/install/>

### Start the container in foreground

```shell
docker-compose up
```

### Start the container in background

```shell
docker-compose up -d
docker-compose logs -f
```

Stop the docker container with `docker-compose down`

## Linting

```shell
yarn lint
yarn lint:styles
yarn format
yarn type-check
```

## PRs

All PRs must pass all checks before they will be considered for review.

## Branching strategy

Inherits the rules from <https://nvie.com/posts/a-successful-git-branching-model/>

Branching model|Merges
---|---
![](https://nvie.com/img/git-model@2x.png)|![](https://nvie.com/img/merge-without-ff@2x.png)

## Translations (i18n)

### Translation namespaces

Default namespace is called `common` and contains translations used on *all pages* (Layout, Nav, etc) and is stored at `frontend/public/locales/{locale}/common.json`

Namespaces (scopes, domains) are stored in separate json files at `frontend/public/locales/{locale}/{namespace}.json`
One namespace can combine the translations keys from several pages with common reusable strings ex. `auth` scope collects keys for `login` and `register` pages. 

### Translation keys

It is preferred to use [kebab-case](https://en.wiktionary.org/wiki/kebab_case) for translation keys and extract another level of nesting when the common prefix of the keys is above 3 or makes sense to be separated as new keys might be added in the future.

- Namespace is separated with `:`
- Translation nesting levels are separated with `.`
- Words in a translation key are separated with `-`

`domain:pages.nested-level.another-nested-level.translation-key`

```json
{
  "cta": {
    "login": "Log In",
    "register": "Register",
    "send": "Send",
    "reset": "Reset"
  },
  "fields": {
    "email": "Email",
    "password": "Password",
    "confirm-password": "Confirm Password",
    "first-name": "First name",
    "last-name": "Last name"
  },
  "pages": {
    "forgotten-password": {
      "instructions": "To reset your password, please type your email address below.",
      "greeting": "Hello {{name}}!"
    }
  }
}
```

### Usage in React

Usage of translation hook `useTranslation` is preferred over usage of `<Trans />` component, whenever possible.

#### Usage in pages

```tsx
import { useTranslation } from 'react-i18next'

export default function CustomPage() {
  const { t } = useTranslation()

  return (
    <div>
      <h1>{t('nav.custom-page')}</h1>
      <h2>{t('auth:pages.forgotten-password.greeting', {name: 'Interpolation'} )}</h2>
      <p>{t('auth:pages.forgotten-password.instructions')}</p>
    </div>
  )
}
```

#### SSR preloading i18n in pages

```tsx
import { GetStaticProps } from 'next'
import { serverSideTranslations } from 'common/useNextLocale'

import Page from 'components/forgottenPassword/ForgottenPasswordPage'

export const getStaticProps: GetStaticProps = async ({ locale }) => ({
  props: {
    i18nResources: await serverSideTranslations(locale, ['common', 'auth']), // List used namespaces
  },
})

export default Page
```