---
title: 소스 플러그인
typora-copy-images-to: ./
disableTableOfContents: true
---

> 이 튜토리얼은 Gatsby 데이터 레이어 시리즈의 일부입니다. 계속하기 전에 [파트 4](/tutorial/part-four/)를 진행했는지 확인하세요.

## 이번 튜토리얼에서 다룰 것

이번 튜토리얼에서는, GraphQL과 소스 플러그인을 사용하여 Gatsby 사이트로 데이터를 가져오는 방법을 배울 것입니다. 그러나 그 플러그인들을 배우기에 앞서, 여러분은 쿼리를 바르게 구성하도록 도와주는 GraphiQL이라는 툴을 어떻게 사용하는지 알고싶을 것입니다.

## GraphiQL 소개

GraphiQL은 GraphQL 통합 개발 환경(IDE)입니다. 이것은 Gatsby 웹사이트를 구축할 때 자주 사용하는 강력한(그리고 놀랍도록 만능의) 툴입니다.

여러분은 사이트의 개발서버가 동작중일 때 <http://localhost:8000/___graphql>로 액세스할 수 있습니다.


<video controls="controls" autoplay="true" loop="true">
  <source type="video/mp4" src="/graphiql-explore.mp4"></source>
  <p>브라우저가 동영상을 지원하지 않습니다.</p>
</video>

Poke around the built-in `Site` "type" and see what fields are available on it -- including the `siteMetadata` object you queried earlier. Try opening GraphiQL and play with your data! Press <kbd>Ctrl + Space</kbd> (or use <kbd>Shift + Space</kbd> as an alternate keyboard shortcut) to bring up the autocomplete window and <kbd>Ctrl + Enter</kbd> to run the GraphQL query. You'll be using GraphiQL a lot more through the remainder of the tutorial.

## GraphiQL Explorer 사용하기

The GraphiQL Explorer enables you to interactively construct full queries by clicking through available fields and inputs without the repetitive process of typing these queries out by hand.
GraphiQL Explorer는 여러분이 

<EggheadEmbed
  lessonLink="https://egghead.io/lessons/gatsby-build-a-graphql-query-using-gatsby-s-graphiql-explorer"
  lessonTitle="Build a GraphQL Query using Gatsby’s GraphiQL Explorer"
/>

## 소스 플러그인

Gatsby 사이트의 데이터는 API, 데이터베이스, CMS, 로컬 파일 등 어디에서든 가져올 수 있습니다.

소스 플러그인은 소스에서 데이터를 가져옵니다. 예) filesystem 소스 플러그인은 파일 시스템에서 데이터를 가져올 수 있습니다. WordPress 플러그인은 WordPress API에서 데이터를 가져올 수 있습니다.

[`gatsby-source-filesystem`](/packages/gatsby-source-filesystem/)를 추가하고 어떻게 작동하는지 살펴봅시다.

먼저, 프로젝트의 루트에 플러그인을 인스톨합니다 :

```shell
npm install --save gatsby-source-filesystem
```

그리고 여러분의 `gatsby-config.js`에 이것을 추가하세요 :

```javascript:title=gatsby-config.js
module.exports = {
  siteMetadata: {
    title: `Pandas Eating Lots`,
  },
  plugins: [
    // highlight-start
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `src`,
        path: `${__dirname}/src/`,
      },
    },
    // highlight-end
    `gatsby-plugin-emotion`,
    {
      resolve: `gatsby-plugin-typography`,
      options: {
        pathToConfigModule: `src/utils/typography`,
      },
    },
  ],
}
```

그것을 저장하고 gatsby 개발 서버를 재시작합니다. 그런 다음 GraphiQL을 다시 여세요.

여러분은 이제 탐색기에서 `allFile`과 `file`을 볼 수 있고 선택도 가능할 것입니다 :

![graphiql-filesystem](graphiql-filesystem.png)

`allFile` 드롭다운을 클릭하세요. 쿼리 영역에서 `allFile` 뒤에 커서를 놓고 <kbd>Ctrl + Enter</kbd>를 누릅니다. 이것은 각 파일의 `id`에 대한 쿼리 미리채우기를 합니다. "Play"를 눌러 쿼리를 실행하세요 :

![filesystem-query](filesystem-query.png)

In the Explorer pane, the `id` field has automatically been selected. Make selections for more fields by checking the field's corresponding checkbox. Press "Play" to run the query again, with the new fields:

![filesystem-explorer-options](filesystem-explorer-options.png)

Alternatively, you can add fields by using the autocomplete shortcut (<kbd>Ctrl + Space</kbd>). This will show queryable fields on the `File` nodes.

![filesystem-autocomplete](filesystem-autocomplete.png)

Try adding a number of fields to your query, pressing <kbd>Ctrl + Enter</kbd>
each time to re-run the query. You'll see the updated query results:

![allfile-query](allfile-query.png)

The result is an array of `File` "nodes" (node is a fancy name for an object in a
"graph"). Each `File` node object has the fields you queried for.

## GraphQL 쿼리를 이용한 페이지 빌드

Building new pages with Gatsby often starts in GraphiQL. You first sketch out
the data query by playing in GraphiQL then copy this to a React page component
to start building the UI.

Let's try this.

Create a new file at `src/pages/my-files.js` with the `allFile` GraphQL query you just
created:

```jsx:title=src/pages/my-files.js
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data) // highlight-line
  return (
    <Layout>
      <div>Hello world</div>
    </Layout>
  )
}

export const query = graphql`
  query {
    allFile {
      edges {
        node {
          relativePath
          prettySize
          extension
          birthTime(fromNow: true)
        }
      }
    }
  }
`
```

The `console.log(data)` line is highlighted above. It's often helpful when
creating a new component to console out the data you're getting from the GraphQL query
so you can explore the data in your browser console while building the UI.

If you visit the new page at `/my-files/` and open up your browser console
you will see something like:

![data-in-console](data-in-console.png)

The shape of the data matches the shape of the GraphQL query.

Add some code to your component to print out the File data.

```jsx:title=src/pages/my-files.js
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data)
  return (
    <Layout>
      {/* highlight-start */}
      <div>
        <h1>My Site's Files</h1>
        <table>
          <thead>
            <tr>
              <th>relativePath</th>
              <th>prettySize</th>
              <th>extension</th>
              <th>birthTime</th>
            </tr>
          </thead>
          <tbody>
            {data.allFile.edges.map(({ node }, index) => (
              <tr key={index}>
                <td>{node.relativePath}</td>
                <td>{node.prettySize}</td>
                <td>{node.extension}</td>
                <td>{node.birthTime}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
      {/* highlight-end */}
    </Layout>
  )
}

export const query = graphql`
  query {
    allFile {
      edges {
        node {
          relativePath
          prettySize
          extension
          birthTime(fromNow: true)
        }
      }
    }
  }
`
```

And now visit [http://localhost:8000/my-files](http://localhost:8000/my-files)… 😲

![my-files-page](my-files-page.png)

## What's coming next?

Now you've learned how source plugins bring data _into_ Gatsby’s data system. In the next tutorial, you'll learn how transformer plugins _transform_ the raw content brought by source plugins. The combination of source plugins and transformer plugins can handle all data sourcing and data transformation you might need when building a Gatsby site. Learn about transformer plugins in [part six of the tutorial](/tutorial/part-six/).
