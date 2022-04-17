# helmfile

## sync
The ```helmfile sync``` sub-command sync your cluster state as described in your helmfile. The default helmfile is helmfile.yaml, but any YAML file can be passed by specifying a ```--file path/to/your/yaml/file``` flag.

Under the covers, Helmfile executes helm upgrade --install for each release declared in the manifest, by optionally decrypting secrets to be consumed as helm chart values. It also updates specified chart repositories and updates the dependencies of any referenced local charts.


## lint
The ```helmfile lint``` sub-command runs a helm lint across all of the charts/releases defined in the manifest. Non local charts will be fetched into a temporary folder which will be deleted once the task is completed.



## Templates

You can use go's text/template expressions in helmfile.yaml and values.yaml.gotmpl (templated helm values files). values.yaml references will be used verbatim. In other words:

for value files ending with .gotmpl, template expressions will be rendered
for plain value files (ending in .yaml), content will be used as-is

In addition to built-in ones, the following custom template functions are available:

readFile reads the specified local file and generate a golang string
fromYaml reads a golang string and generates a map
setValueAtPath PATH NEW_VALUE traverses a golang map, replaces the value at the PATH with NEW_VALUE
toYaml marshals a map into a string
get returns the value of the specified key if present in the .Values object, otherwise will return the default value defined in the function


## Values Files Templates

You can reference a template of values file in your helmfile.yaml like below:
```
releases:
- name: myapp
  chart: mychart
  values:
  - values.yaml.gotmpl
```

Every values file whose file extension is .gotmpl is considered as a template file.

Suppose values.yaml.gotmpl was something like:
```
{{ readFile "values.yaml" | fromYaml | setValueAtPath "foo.bar" "FOO_BAR" | toYaml }}
```
And values.yaml was:
```
foo:
  bar: ""
```