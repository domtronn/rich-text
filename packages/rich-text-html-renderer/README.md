# rich-text-html-renderer

HTML renderer for the Rich Text document.

## Installation

Using [npm](http://npmjs.org/):

```javascript
npm install @contentful/rich-text-html-renderer
```

Using [yarn](https://yarnpkg.com/):

```javascript
yarn add @contentful/rich-text-html-renderer
```

## Usage

```javascript
import { documentToHtmlString } from '@contentful/rich-text-html-renderer';

const document = {
  nodeType: 'document',
  content: [
    {
      nodeType: 'paragraph',
      content: [
        {
          nodeType: 'text',
          value: 'Hello world!',
          marks: [],
        },
      ],
    },
  ],
};

documentToHtmlString(document); // -> <p>Hello world!</p>
```

```javascript
import { documentToHtmlString } from '@contentful/rich-text-html-renderer';

const document = {
  nodeType: 'document',
  content: [
    {
      nodeType: 'paragraph',
      content: [
        {
          nodeType: 'text',
          value: 'Hello',
          marks: [{ nodeType: 'bold' }],
        },
        {
          nodeType: 'text',
          value: ' world!',
          marks: [{ nodeType: 'italic' }],
        },
      ],
    },
  ],
};

documentToHtmlString(document); // -> <p><b>Hello</b><u> world!</u></p>
```

You can also pass custom renderers for both marks and nodes as an optional parameter like so:

```javascript
import { BLOCKS.PARAGRAPH, MARKS.BOLD } from '@contentful/rich-text-types';
import { documentToHtmlString } from '@contentful/rich-text-html-renderer';

const document = {
  nodeType: 'document',
  content: [
    {
      nodeType: 'paragraph',
      content: [
        {
          nodeType: 'text',
          value: 'Hello',
          marks: [{ nodeType: 'bold' }]
        },
        {
          nodeType: 'text',
          value: ' world!',
          marks: [{ nodeType: 'italic' }]
        },
      ],
    },
  ]
};

const options = {
  renderMark: {
    [MARKS.BOLD]: text => `<custom-bold>${text}<custom-bold>`
  },
  renderNode: {
    [BLOCKS.PARAGRAPH]: (node, next) => `<custom-paragraph>${next(node.content)}</custom-paragraph>`
  }
}

documentToHtmlString(document, options);
// -> <custom-paragraph><custom-bold>Hello</custom-bold><u> world!</u></custom-paragraph>
```

Last, but not least, you can pass a custom rendering component for an embedded entry:

```javascript
import { BLOCKS.EMBEDDED_ENTRY } from '@contentful/rich-text-types';
import { documentToHtmlString } from '@contentful/rich-text-html-renderer';

const document = {
  nodeType: 'document',
  content: [
    {
      nodeType: 'embedded-entry-block',
      data: {
        target: (...)Link<'Entry'>(...);
      },
    },
  ]
};

const options = {
  renderNode: {
    [BLOCKS.EMBEDDED_ENTRY]: (node) => `<custom-component>${customComponentRenderer(node)}</custom-component>`
  }
}

documentToHtmlString(document, options);
// -> <custom-component>(...)Link<'Entry'>(...)</custom-component>
```

The `renderNode` keys should be one of the following `BLOCKS` and `INLINES` properties as defined in [`@contentful/rich-text-types`](https://www.npmjs.com/package/@contentful/rich-text-types):

- `BLOCKS`
  - `DOCUMENT`
  - `PARAGRAPH`
  - `HEADING_1`
  - `HEADING_2`
  - `HEADING_3`
  - `HEADING_4`
  - `HEADING_5`
  - `HEADING_6`
  - `UL_LIST`
  - `OL_LIST`
  - `LIST_ITEM`
  - `QUOTE`
  - `HR`
  - `EMBEDDED_ENTRY`
  - `EMBEDDED_ASSET`

- `INLINES`
  - `EMBEDDED_ENTRY` (this is different from the `BLOCKS.EMBEDDED_ENTRY`)
  - `HYPERLINK`
  - `ENTRY_HYPERLINK`
  - `ASSET_HYPERLINK`

The `renderMark` keys should be one of the following `MARKS` properties as defined in [`@contentful/rich-text-types`](https://www.npmjs.com/package/@contentful/rich-text-types):

- `BOLD`
- `ITALIC`
- `UNDERLINE`
- `CODE`