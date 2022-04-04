<!--
title:   【React】eslintの設定 備忘録
tags:    ESLint,React,TypeScript,prettier
id:      fe0fe6b962bcebf3dacf
private: false
-->

# 初めに

React では eslint を使うのが一般的になってきています。その際に設定をいちいち考えるのなかなかめんどくさいので、忘備録としてここに残しておきます。
typescript・prettie 対応です。

# eslintrc.js

```js:.eslintrc.js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'plugin:react/recommended',
    'airbnb',
    'airbnb/hooks',
    'plugin:import/errors',
    'plugin:import/warnings',
    'plugin:import/typescript',
    'plugin:cypress/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking',
    'prettier',
  ],
  globals: {
    cy: 'readonly',
    Cypress: 'readonly',
  },
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    project: './tsconfig.eslint.json',
    sourceType: 'module',
    tsconfigRootDir: __dirname,
  },
  plugins: [
    '@typescript-eslint',
    'import',
    'jsx-a11y',
    'prefer-arrow',
    'react',
    'react-hooks',
    'cypress',
  ],
  root: true,
  rules: {
    'no-use-before-define': 'off',
    '@typescript-eslint/no-use-before-define': ['error'],
    'lines-between-class-members': [
      'error',
      'always',
      {
        exceptAfterSingleLine: true,
      },
    ],
    'no-void': [
      'error',
      {
        allowAsStatement: true,
      },
    ],
    'padding-line-between-statements': [
      'error',
      {
        blankLine: 'always',
        prev: '*',
        next: 'return',
      },
    ],
    '@typescript-eslint/no-unused-vars': [
      'error',
      {
        vars: 'all',
        args: 'after-used',
        argsIgnorePattern: '_',
        ignoreRestSiblings: false,
        varsIgnorePattern: '_',
      },
    ],
    'import/extensions': [
      'error',
      'ignorePackages',
      {
        js: 'never',
        jsx: 'never',
        ts: 'never',
        tsx: 'never',
      },
    ],
    'import/no-extraneous-dependencies': [
      'error',
      {
        devDependencies: [
          '**/setupTests.ts',
          '.storybook/**',
          'stories/**',
          '**/*/*.story.*',
          '**/*/*.stories.*',
          '**/__specs__/**',
          '**/*/*.spec.*',
          '**/__tests__/**',
          '**/*/*.test.*',
        ],
      },
    ],
    'prefer-arrow/prefer-arrow-functions': [
      'error',
      {
        disallowPrototype: true,
        singleReturnOnly: false,
        classPropertiesAllowed: false,
      },
    ],
    'react/jsx-filename-extension': [
      'error',
      {
        extensions: ['.jsx', '.tsx'],
      },
    ],
    'react/jsx-props-no-spreading': [
      'error',
      {
        html: 'enforce',
        custom: 'enforce',
        explicitSpread: 'ignore',
      },
    ],
    'react/react-in-jsx-scope': 'off',
    'react-hooks/rules-of-hooks': 'error',
    'react/require-default-props': [0],
    'react-hooks/exhaustive-deps': [
      'warn',
      {
        additionalHooks: 'useRecoilCallback',
      },
    ],
  },
  overrides: [
    {
      files: ['*.tsx'],
      rules: {
        'react/prop-types': 'off',
      },
    },
  ],
  settings: {
    'import/resolver': {
      node: {
        paths: ['src/'],
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
    },
  },
};

```

# .eslintignore

```eslintignore:.eslintignore
build/
public/
**/coverage/
**/node_modules/
**/*.min.js
*.config.js
.*lintrc.js
.eslintrc.js
cypress/plugins/
cypress/integration/
cypress/support/

```

# .stylelintrc.js

```js:.styleintrc.js
module.exports = {
  extends: ['stylelint-config-standard', 'stylelint-config-recess-order'],
  plugins: ['stylelint-order'],
  ignoreFiles: ['**/node_modules/**'],
  rules: {
    'string-quotes': 'single',
  },
};

```

# .prettierrc

```prettier:.prettierrc
singleQuote: true
trailingComma: "all"
```

# tsconfig.eslint.json

```json:tsconfig.eslint.json
{
  "extends": "./tsconfig.json",
  "include": [
    "src/**/*.js",
    "src/**/*.jsx",
    "src/**/*.ts",
    "src/**/*.tsx",
  ],
  "exclude": [
    "node_modules"
  ]
}

```

# tsconfig.json

```json:tsconfig.json
{
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext",
      "es5"
    ],
    "types": ["jest"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "baseUrl": "src",
    "downlevelIteration": true
  },
  "include": [
    "src",
  ]
}

```

# package.json

```json:package.json
{
  "name": "coffee-oma",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@hookform/error-message": "^2.0.0",
    "@storybook/react": "^6.2.9",
    "@testing-library/jest-dom": "^5.11.10",
    "@testing-library/react": "^11.2.5",
    "@testing-library/user-event": "^13.0.16",
    "@types/jest": "^26.0.22",
    "@types/node": "^14.14.37",
    "@types/react": "^17.0.3",
    "@types/react-dom": "^17.0.3",
    "@types/react-router": "^5.1.18",
    "@types/react-router-dom": "^5.3.3",
    "@types/recoil": "^0.0.1",
    "axios": "^0.21.1",
    "browserslist": "^4.18.0",
    "dayjs": "^1.10.4",
    "eslint-config-react-app": "^6.0.0",
    "framer-motion": "^4.1.17",
    "history": "5",
    "react": "^17.0.2",
    "react-cookie": "^4.1.1",
    "react-docgen-typescript-webpack-plugin": "^1.1.0",
    "react-dom": "^17.0.2",
    "react-helmet": "^6.1.0",
    "react-hook-form": "^7.4.0",
    "react-loader-spinner": "^4.0.0",
    "react-query": "^3.32.1",
    "react-rating": "^2.0.5",
    "react-router": "6.2.1",
    "react-router-dom": "6.2.1",
    "react-scripts": "4.0.3",
    "react-simple-star-rating": "^4.0.0",
    "recoil": "^0.2.0",
    "semantic-ui-css": "^2.4.1",
    "semantic-ui-react": "^2.0.3",
    "styled-components": "^5.2.3",
    "typescript": "^4.2.3",
    "use-file-upload": "^1.0.9",
    "use-state-if-mounted": "^1.0.4",
    "web-vitals": "^1.1.1"
  },
  "scripts": {
    "start": "REACT_APP_NODE_ENV=development react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "heroku:start": "react-scripts build && react-scripts start",
    "fix": "npm run -s format && npm run -s lint:fix",
    "format": "prettier --write --loglevel=warn 'src/**/*.{js,jsx,ts,tsx,gql,graphql,json}'",
    "lint": "npm run -s lint:style; npm run -s lint:es",
    "lint:fix": "npm run -s lint:style:fix && npm run -s lint:es:fix",
    "lint:es": "eslint 'src/**/*.{js,jsx,ts,tsx}'",
    "lint:es:fix": "eslint --fix 'src/**/*.{js,jsx,ts,tsx}'",
    "lint:conflict": "eslint-config-prettier 'src/**/*.{js,jsx,ts,tsx}'",
    "lint:style": "stylelint 'src/**/*.{css,less,sass,scss}'",
    "lint:style:fix": "stylelint --fix 'src/**/*.{css,less,sass,scss}'",
    "preinstall": "typesync || :",
    "storybool": "start-storybook -p 6006",
    "build:storybook": "build-storybook -o build/stroybook",
    "storybook": "start-storybook -p 6006 -s public",
    "build-storybook": "build-storybook -s public",
    "cypress": "cypress open",
    "cypress:run": "cypress run",
    "start:ci": "CHOKIDAR_USEPOLLING=true react-scripts start",
    "cy:ci": "start-server-and-test start http://localhost:3000 cypress:run"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ],
    "overrides": [
      {
        "files": [
          "**/*.stories.*"
        ],
        "rules": {
          "import/no-anonymous-default-export": "off"
        }
      }
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "jest": {
    "resetMocks": false
  },
  "devDependencies": {
    "@storybook/addon-a11y": "^6.2.9",
    "@storybook/addon-actions": "^6.2.9",
    "@storybook/addon-console": "^1.2.3",
    "@storybook/addon-controls": "^6.2.9",
    "@storybook/addon-docs": "^6.2.9",
    "@storybook/addon-essentials": "^6.2.9",
    "@storybook/addon-info": "^5.3.21",
    "@storybook/addon-knobs": "^6.2.9",
    "@storybook/addon-links": "^6.2.9",
    "@storybook/addon-storyshots": "^6.2.9",
    "@storybook/addon-storyshots-puppeteer": "^6.2.9",
    "@storybook/addon-storysource": "^6.2.9",
    "@storybook/addon-viewport": "^6.2.9",
    "@storybook/node-logger": "^6.2.9",
    "@storybook/preset-create-react-app": "^3.1.7",
    "@types/babel-plugin-macros": "^2.8.4",
    "@types/prettier": "^2.2.3",
    "@types/react-helmet": "^6.1.1",
    "@types/react-test-renderer": "^17.0.1",
    "@types/storybook-react-router": "^1.0.1",
    "@types/storybook__addon-actions": "^5.2.1",
    "@types/storybook__addon-info": "^5.2.3",
    "@types/storybook__addon-knobs": "^5.2.1",
    "@types/storybook__react": "^5.2.1",
    "@types/styled-components": "^5.1.9",
    "@types/stylelint": "^9.10.1",
    "@types/testing-library__jest-dom": "^5.9.5",
    "@types/testing-library__user-event": "^4.2.0",
    "@typescript-eslint/eslint-plugin": "^4.20.0",
    "@typescript-eslint/parser": "^4.20.0",
    "axios-mock-adapter": "^1.20.0",
    "babel-loader": "^8.2.2",
    "babel-plugin-macros": "^3.1.0",
    "cypress": "^8.7.0",
    "eslint-config-airbnb": "18.2.1",
    "eslint-config-prettier": "^8.1.0",
    "eslint-plugin-cypress": "^2.12.1",
    "eslint-plugin-import": "^2.22.1",
    "eslint-plugin-jsx-a11y": "^6.4.1",
    "eslint-plugin-prefer-arrow": "^1.2.3",
    "eslint-plugin-react": "^7.23.1",
    "eslint-plugin-react-hooks": "4.2.0",
    "jest": "^27.0.1",
    "jest-localstorage-mock": "^2.4.13",
    "jest-watch-typeahead": "^0.6.4",
    "prettier": "^2.2.1",
    "puppeteer": "^10.0.0",
    "react-docgen-typescript-loader": "^3.7.2",
    "react-test-renderer": "^17.0.2",
    "require-context.macro": "^1.2.2",
    "start-server-and-test": "^1.14.0",
    "storybook-addon-mock": "^0.1.0",
    "storybook-axios": "^1.0.1",
    "storybook-react-router": "^1.0.8",
    "stylelint": "^13.12.0",
    "stylelint-config-recess-order": "^2.3.0",
    "stylelint-config-standard": "^21.0.0",
    "stylelint-order": "^4.1.0",
    "typescript-styled-plugin": "^0.15.0",
    "typesync": "^0.8.0"
  }
}

```

# 参考

下記の書籍を読んで学べば、React の事は完全に理解できると思います

[りあクト](https://techbookfest.org/product/5436045076201472?productVariantID=6240785991204864)
