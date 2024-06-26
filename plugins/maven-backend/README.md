## Maven actions

This plugin provides the following list of backstage action(s) to be used in a template:

| Action                     | Description                                                                                                                                                |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `maven:dependencies:add`   | Add one or more maven dependencies to a pom in your project                                                                                                |

To use this plugin, add the following packages to the backstage backend:
```bash
yarn add --cwd packages/backend "@qshift/plugin-maven-backend"
yarn add --cwd packages/backend "@backstage/integration"
```
Next, follow the instructions documented for each `action`

### maven:dependencies:add

To use this action able to add maven dependencies to a POM xml file, add it to the new backend system like this

```typescript
// packages/backend/src/index.ts
import { createBackend } from '@backstage/backend-defaults';

const backend = createBackend();
...
backend.add(import('@qshift/plugin-quarkus-backend'))
...
backend.start()
```

or edit the file `packages/backend/src/plugins/scaffolder.ts` using the old backend system to register the action: `mavenDependenciesAdd`.

Here is a snippet example of code changed
```typescript
import { ScmIntegrations } from '@backstage/integration';
import {createBuiltinActions, createRouter} from '@backstage/plugin-scaffolder-backend';
import { mavenDependenciesAdd } from '@internal/plugin-maven-backend';
...
  const integrations = ScmIntegrations.fromConfig(env.config);

  const builtInActions = createBuiltinActions({
    integrations,
    catalogClient,
    config: env.config,
    reader: env.reader,
  });

  const actions = [...builtInActions, mavenDependenciesAdd()];

  return await createRouter({
    actions,
```

The following table details the fields that you can use to customize this action:

| Input                | Description                             | Type               | Required |
|--------------------- |-----------------------------------------|--------------------|----------|
| pomPath              | The path of the pom.xml file to edit    | string             | Yes      |
| dependencies         | Maven dependencies                      | object (see below) | No       |


The `dependencies` field is an array of objects with the following fields:
| Field                | Description                             | Type               | Required |
|--------------------- |-----------------------------------------|--------------------|----------|
| groupId              | Maven GroupID                           | string             | Yes      |
| artifactId           | Maven ArtifactID                        | string             | Yes      |
| version              | Maven version                           | string             | No       |
| scope                | Maven scope                             | string             | No       |
| classifier           | Maven classifier                        | string             | No       |
| optional             | Maven optional                          | boolean            | No       |

Example of action:
```yaml
steps:
  - action: maven:dependencies:add
    id: maven-dependency-add
    name: Add dependencies to a Maven project
    input:
      values:
        dependencies:
          - groupId: org.mvnpm.at.mvnpm
            artifactId: vaadin-webcomponents
            version: 24.3.10
            scope: provided
```
