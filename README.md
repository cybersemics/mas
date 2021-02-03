# Metaprogramming Attribute Specification

The **Metaprogramming Attribute Specification (mas)** is a simple data-and-interaction specification for text-based hierarchical editors that allows *Meta Attributes* such as styles, settings, and behaviors to be defined, edited, and shared as first-class citizens.

## Summary

In a document that conforms to **mas**, *Meta Attributes* are:

- Edited the same way that document content is edited.
- Imported and exported with document content.
- Defined at any level of the hierarchy.
- Applied to parent, children, or all descendants.
- Scoped by depth, i.e. descendants override ancestors.
- Applicable to text-based trees or graphs.

## Example

1. Create a simple outline:

    - Journal
      - 2021-02-03
        - mas rough draft published

2. *Meta Attributes* can be edited *in-band*, as first-class citizens:

    - Journal
      - `#meta`
        - `expand`
        - `style`
          - fontWeight
            - bold
          - listStyle
            - none
      - 2021-02-03
        - mas rough draft published

3. This will make "Journal" bold and expanded, and remove bullets from its children:

    - **Journal**
      <br>2021-02-03
        - mas rough draft published

4. It may be exported using a **mas**-conformant data representation:

    ```json
    {
      "#mas": {
        "version": "v0.1.0"
      },
      "children": [
        {
          "text": "Journal",
          "meta": {
            "expand": true,
            "style": {
              "fontWeight": "bold",
              "listStyle": "none"
            },
          },
          "children": [
            {
              "text": "2021-02-03",
              "children": [
                {
                  "text": "mas rough draft published"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

## Meta Attributes

- *Meta Attributes* may take 0 or more arguments. 
- Arguments are specified as children:
  - `attr`
    - arg1
    - arg2
    - arg3
- Named arguments are specified as children and grandchildren:
  - `style`
    - `fontWeight`
      - bold
    - `listStyle`
      - none
- A leaf attribute is a *Boolean* attribute and equivalent to a single argument `true`
  - `expand`
    - `true` (may be omitted)

*Meta Attributes* are organized into modules. All clients must at minimum conform to the Basic module. If an attribute is not specified, the client may choose the default behavior.

### Basic

- **children** - Applies *Meta Attributes* to all of the item's children.
- **descendants** - Applies *Meta Attributes* to all of the item's descendants.
- **expand** - Control the expansion of the item. Options: `true`, `false`, `auto`.
- **hidden** - Hides the item.
- **style** - Applies inline CSS styles.

### Change Tracking

- **createdAt** - A UTC timestamp of the item's creation item.
- **createdBy** - The person who created the item.
- **editedAt** - A UTC timestamp of the item's creation item.
- **editedBy** - The person who created the item.
- **version** - The version of the item.

### Miscellaneous

- **drop** - Controls where dropped items are placed relative to siblings when the item is collapsed. Options: `top`, `bottom`.
- **label** - Display alternative text, but continue using the original text for linking, querying, and editing.
- **note** - Defines a small note to be rendered beneath the item.

### Validation

- **immovable** - The item cannot be moved.
- **options** - A list of allowed children.
- **readonly** - The item cannot be moved, deleted, edited, or descendants added. Equivalent to **immoveable**, **undeletable**, **uneditable**, and **unextendable**.
- **undeletable** - The item cannot be deleted.
- **uneditable** - The item cannot be edited.
- **unextendable** - The item cannot be moved.

### Views

Each value of **view** is treated as a separate module in the spec.

- **view** - Controls how the item and its descendants are displayed. Options: `article`, `graph`, `list`, `prose`, `table`.

## Other Formats

The default data format is `JSON` in order to encourage interoperability. Other formats may be used with an appropriate transformation function.

### Markdown

```md
# mas v0.1.0
- Journal
  - #meta
    - expand
    - style
      - fontWeight
        - bold
      - listStyle
        - none
  - 2021-02-03
    - mas rough draft published
```

### XML

```xml
<!DOCTYPE mas v0.1.0>
<ul>
  <li>Journal<ul>
    <li>#meta<ul>
      <li>expand<li>
      <li>style<ul>
        <li>fontWeight<ul>
          <li>bold</li>
        </ul></li>
        <li>=listStyle<ul>
          <li>none</li>
        </ul></li>
      </ul></li>
    </ul></li>
    <li>2021-02-03<ul>
      <li>mas rough draft published</li>
    </ul></li>
  </ul></li>
</ul>
```

## Questions

- How to decouple from the selector specification? We don't want to reinvent CSS (much less an impoverished version of it). Yet the **children** meta attribute (i.e. child selector) is essential for practical use.
- Is there any way to decouple from the outliner format? There are other outliner formats that have been created, which we don't necessarily want to compete with.
