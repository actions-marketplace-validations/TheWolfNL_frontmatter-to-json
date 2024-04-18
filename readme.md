# Frontmatter to JSON #

This is a GitHub Action that converts the frontmatter in a file or content string to a JSON string.


## Inputs ##

* `content`: content as string.
* `file`: file path as string, it's your responsibility to checkout the code or make sure the file exists.

## Outputs ##

* `json`: JSON string containing frontmatter.

## Examples ##

<details>
<summary>Extract frontmatter from content</summary>

```yaml
...
  - id: content
    name: Extract frontmatter from content
    uses: TheWolfNL/frontmatter-to-json@master
    with:
      content: '---\ntitle: lorem ipsum\ndescription: hello world\nnumber: 42\ndate: 2024-12-31\n---\n\n# Header 1 #\n\nParagraph 1'

  - name: Output JSON
    shell: bash
    run: |
      echo '${{ steps.content.outputs.json }}' | jq
...
```

</details>

<details>
<summary>Extract frontmatter from file</summary>

```yaml
...
  - id: file
    name: Extract frontmatter from file
    uses: TheWolfNL/frontmatter-to-json@master
    with:
      file: 'samples/simple.md'

  - name: Output JSON
    shell: bash
    run: |
      echo '${{ steps.file.outputs.json }}' | jq
...
```

</details>

## Usage ##

<details>
<summary>Use string value from frontmatter</summary>

```yaml
...
  - id: file
    name: Extract frontmatter from file
    uses: TheWolfNL/frontmatter-to-json@master
    with:
      file: 'samples/simple.md'

  - name: Output JSON
    shell: bash
    run: echo '${{ fromJson(steps.file.outputs.json).title }}' | jq
...
```

</details>

<details>
<summary>Use List as input for Matrix</summary>

```yaml
...
jobs:
  data:
    runs-on: ubuntu-latest
    name: Extract list from frontmatter
    permissions:
      contents: read  # This is required for actions/checkout
    outputs:
      keys: ${{ fromJson(steps.file.outputs.json).keys }}
    steps:
      - uses: actions/checkout@v4
      - id: file
        name: Extract frontmatter from file
        uses: TheWolfNL/frontmatter-to-json@master
        with:
            file: 'samples/list.md'
  keys:
    runs-on: ubuntu-latest
    name: Keys
    needs: data
    if: needs.data.outputs.keys != '[]'
    strategy:
      matrix: 
        key: ${{ fromJson(needs.data.outputs.keys) }}
    steps:
      - name: output
        shell: bash
        run: |
            echo Key: '${{ matrix.key }}'
...
```

</details>