# NextJS

## Why Next.js

- Pre-Rendering
- Static Exporting
- CSS-in-JS
- Zero Configuration
- Fully Extensible
- Ready for Production

## Installing NextJS

- creating a new project

```bash
npx create-next-app hello-next --use-npm
```

- convert all the js and jsx files to ts and tsx
- install devDependencies typescript, @types/node and @types/react
- `npm run dev`

## File structure explanation

- files and folders inside `public` folder are directly accessible.
- you can move the `styles` folder anywhere you like, inside `public` or `pages`.
- You can also create a `src` folder and put your `styles` and `pages` folder inside `src`.

## Pages in NextJS

- `index.ts` is available at your root url.
- create a react component inside the `pages` folder and it will be available at a relative url.

## _document.tsx

- It is the root file and can used to injecting meta tags.
- You can also inject scripts.
- It is used to create the overall structure.
- codes are executed only on the server-side.

```jsx
import { Html, Head, Main, NextScript } from 'next/document'

export default class CustomDocument extends Document {
	render() {
		console.log('hello from _document.tsx')
		return <Html>
			<Head>
				<meta property="custom" content="yolo" />
			</Head>
			<body>
				<Main />
			</body>
			<NextScript />
		</Html>
	}
}
```

## _app.tsx

- It is responsible for rendering all of your pages.
- With `_document.tsx` things will only be executed in the server-side.
- But with `_app.tsx` things will be executed on the server-side as well as on the client-side.

```jsx
export default function App({ Component, pageProps }) {
	console.log('hello from _app.tsx')
	return <Component {...pageProps} />
}
```

## Styled-JSX

```jsx
// Styled-JSX
function Heading(props) {
	const color = Math.random() > 0.5 ? 'red' : 'blue'
	return (
		<div>
			<h1>{props.heading}</h1>
			<style jsx global>
				{`
					h1 {
						color: ${color};
					}
				`}
			</style>
		</div>
	)
}

export default function Home() {
	return (
		<div>
			<Heading heading="Heading" />
			<h1> Something else </h1>
		</div>
	)
}
```

## Global Styles

- importing global css files is only possible in `_app.tsx.`

```jsx
// _app.tsx
import './main.css'
```

## CSS Modules

- If you want to import a css file only restricted to a particular component, then name the css file as `*.module.css` and import it.

```jsx
// circle.tsx
import './circle.module.css'
```

## Sass Modules

- NextJS support Sass natively.
- You need to install sass as a devDependency. 
`npm install -D sass`
- Create files using scss extension and import them as you would normally import css files.

```jsx
// circle.tsx
import './circle.module.scss'
```

## File structure

- You can structure your pages in a folder and keep the css or scss files in that particular folder.
- It looks clean to use the folder structure.
- The route will be the `/folderName`.

```jsx
+ -- your-project/
| -- + -- pages/
| -- + -- + -- _app.tsx
| -- + -- + -- login/
| -- + -- + -- + -- index.tsx
| -- + -- + -- + -- index.module.css
```

## Creating APIs with NextJS

- The api files inside api folder shouldn't react component.
- It is used to return json responses mainly.

```jsx
// api/random-number.ts
export default function (req: NextApiRequest, res: NextApiResponse) {
	res.json({ status: 'ok', num: Math.floor(Math.random() * 10) })
}

// http://localhost:3000/api/random-number
```

## req and res Objects

- res.json({})
- req.body
- req.headers()
- req.query()
- res.send()
- res.setHeader('X-Custom-Header', 'we are open to hire people')
- res.setHeader('Set-Cookie', 'areyouaprogrammer-true')
- res.statusCode = 200
- res.end('Hello! how are you')

## Simple JWT Auth example

- `npm i jsonwebtoken`
- `npm i -D @types/jsonwebtoken`

```jsx
// pages/index.tsx
export default function Home() {
  return (
    <>
      <form action="/api/login" method="post">
        <label>id</label>
        <input type="text" name="username" defaultValue="admin" />
        <label>password</label>
        <input type="password" name="password" />
        <button type="submit">submit</button>
      </form>
    </>
  )
}
```

```jsx
// pages/api/login.ts
import { NextApiRequest, NextApiResponse } from "next"
import jwt from 'jsonwebtoken'

const KEY = 'randomkeyonthekeyboard'

export default (req: NextApiRequest, res: NextApiResponse) => {
  if (!req.body) {
    res.statusCode = 404
    res.end()
  }

  const { username, password } = req.body
  res.json({
    token: jwt.sign({
      username,
      admin: username === 'admin' && password === 'password'
    }, KEY)
  })
}
```

## Simple Routing

- All the main react files inside pages should return a valid react element.

### Link Component

```jsx
import Link from 'next/link'

<Link href="/irfan">
	<a> My Blog<a/>
</Link>
```

### Nested Routing

- create a folder for nested routing.

```jsx
import Link from 'next/link'
<Link href="/irfan/activities">
	<a> My Activities </a>
</Link>
```

```jsx
+ -- project/
| -- + -- pages/
| -- + -- + irfan/
| -- + -- + -- index.tsx
| -- + -- + -- activities.tsx
```

### Dynamic Routing

- can create a special file `[name].tsx`
- can create a special folder `[folder]`

```jsx
+ -- project/
| -- + -- pages/
| -- + -- + fruit/
| -- + -- + -- [name]/
| -- + -- + -- + -- [subname].tsx
| -- + -- + -- + -- index.tsx
```

## useRouter hook

- get the dynamic query information with the help of this hook.
- In the first render router.query will be an empty object.

```jsx
import { useRouter } from 'next/router'

export default () => {
	const router = useRouter()
	console.log(router)

	const takeMeHome = () => {
		// router.replace('/')
		router.push('/')
	}

	return (
		<h1>
			Hello {router.query.name} {router.query.subname}
			<button onClick={takeMeHome}> Home </button>
		</h1>
	)
}
```

- router.push('/url') pushes the location on the top of history stack.
- router.replace('url') replaces the top of the history stack with the passed url.
- tip: use Link as much as you can for better SEO.

## Catch All Routes

```jsx
+ -- pages
| -- + fruit/
| -- + -- [...name].tsx
```

```jsx
// pages/fruit/[...name].tsx
import { useRouter } from 'next/router'

export default () => {
	const router = useRouter()
	
	if (router.isFallback) return <h1>Loading...</h1>
	
	console.log(router.query)
	return <h1> Hello </h1>
}
```

- to catch the base domain inside a folder, you can create the file named as `[[...name]].tsx`

## Custom 404 Page

- file name should be 404.tsx for each folder or base folder.

```jsx
export default () => {
	return <h1>404</h1>
}
```

## getStaticProps

- execution happens on server-side.
- getStaticProps runs at BUILD TIME.
- It doesn't run at RUNTIME.

```jsx
import { GetStaticProps } from 'next'
import path from 'path'

export const getStaticProps: GetStaticProps = async (context) => {
	const fs = require('fs')
	const txt = fs.readFileSync(path.join(process.cwd(), 'public/robots.txt'), 'utf8')
	return {
		props: {
			revalidate: 10,
			myFavNum: txt
		}
	}
}
```

- revalidate parameter is very important when you pushing your code for production.
- It recreates the static files every given number of seconds and serves it to the users.

## getStaticPaths

- You need to implement it for dynamic path files.

```jsx
import { GetStaticPaths } from 'next'

export const getStaticPaths: GetStaticPaths = async() => {
	return {
		paths: [{ params: { name: 'hello' } }, { params: { name: 'world' } }],
		fallback: true
	}
}
```

## getServerSideProps

- it is called always even on production.

```jsx
export const getServerSideProps: GetServerSideProps = async(context) => {
	return {
		props: {
			myFavNum: Math.random()
		}
	}
}
```