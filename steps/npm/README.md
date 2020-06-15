# npm

This step container runs an [NPM command](https://docs.npmjs.com/cli-documentation/). An NPM step with no configuration will run `npm test` using the default settings as set by NPM. You can define any flags on a command; those listed in the step specifications are only suggestions.

## Specifications

| Setting | Child setting | Data type | Description | Relevant commands | Default | Required |
|---------|---------------|-----------|-------------|-------------------|---------|----------|
| `command` || string | A command to run. | any | `test` | False |
| `packageFolder` || string | Location of the folder containing a package.json file in its root. May be ignored depending on command. | build, publish| ``./`` | False |
| `flags` || mapping | Optional flags to set before running command. | publish | The following defaults are set by NPM | False |
|| `tag` | string | Registers the published package with the given tag, such that `npm install @` will install this version. | publish | `latest` | False |
|| `access` | string | Tells the registry whether this package should be published as public or restricted. Only applies to scoped packages. If you don’t have a paid account, you must publish with `public` to publish scoped packages. | publish | `restricted` | False |
|| `otp` | string | If you have two-factor authentication enabled in auth-and-writes mode then you can provide a code from your authenticator with this. If you don’t include this and you’re running from a TTY then you’ll be prompted. | publish | `null` | False |
|| `dryRun` | boolean | As of `npm@6`, does everything publish would do except actually publishing to the registry. Reports the details of what would have been published. | publish | `false` | False |
| `git` || mapping | A map of git configuration. If you're using HTTPS, only `name` and `repository` are required. || None | True |
|| `ssh_key` | string | The SSH key to use when cloning the git repository. You can pass the key to Nebula as a secret. See the example below. || None | False |
|| `known_hosts` | string | SSH known hosts file. Use a Nebula secret to pass the contents of the file into the workflow as a base64-encoded string. See the example below. || None | False |
|| `name` | string | A directory name for the git clone. || None | False |
|| `branch` | string | The Git branch to clone or reference for the build. || `master` | False |
|| `repository` | string | The git repository URL. || None | False |
| npm || mapping | NPM credentials || None | Certain commands like `publish` require NPM login. NPM login requires a token. |
|| token | string | NPM token created via `npm token create` || None | False |

## Examples

```yaml
steps:
# build
- name: npm
  spec:
    command: build
    git:
      name: design-system,
      repository: https://github.com/puppetlabs/design-system.git
# test
- name: npm
  spec:
    command: test
    git:
      name: design-system,
      repository: https://github.com/puppetlabs/design-system.git
# publish
- name: npm
  spec:
    command: publish
    flags:
      tag: latest
      access: public
      otp: null
      dry-run: false
    git:
      name: design-system,
      repository: https://github.com/puppetlabs/design-system.git
    npm:
      token:
        $type: Secret
        name: npm_token
# ls
- name: npm
  spec:
    command: ls
    git:
      name: design-system,
      repository: https://github.com/puppetlabs/design-system.git
    packageFolder: packages/react-components
    flags:
      json: true
```