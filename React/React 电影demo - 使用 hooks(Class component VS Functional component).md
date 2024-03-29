
>基于电影API,结合 Reactjs 的 hooks 功能,制作的 demo.  
> 参考文章 [how-to-build-a-movie-search-app-using-react-hooks](https://user-gold-cdn.xitu.io/2019/11/16/16e74d99a93e02c6),也能算得上一篇翻译吧,但是主要是根据文中的内容,自己实际来做了一个真正的 demo 出来,还是有一些扩展的.    

> 在线演示[地址](https://frankwang1991.github.io/movies-react-hooks)

## 1. 生成 app

使用 `create-react-app movies-react-hooks` 初始化 app,如果没有安装 `create-react-app` 那么可以使用:  

``` terminal
npm install -g create-react-app
```

来进行安装.  
生成之后我们将看到 ![项目目录结构](https://user-gold-cdn.xitu.io/2019/11/12/16e6038bc8dd9ad9?w=309&h=480&f=png&s=45138)
有了基础目录结构,我们就可以开始了.  

## 2. 准备工作与Header组件  

在这个项目中我们需要四个组件:  

- App.js
- Header.js
- Movie.js
- Search.js

现在,我们在 `src` 文件夹内创建一个新的文件夹 `src/components` 文件夹,然后将 `src/App.js` 文件移动到 `src/components/App.js`(如果这时候运行项目,注意 `App.js`文件中的引用的路径),并且创建一个新的文件 `src/components/Header.js`:  

``` JavaScript
// Header.js
import React from "react";

const Header = (props) => {
	return (
		<header classNames="App-header">
			<h2>{props.text}</h2>
		</header>
	);
};

export default Header;
```  
同时,我们修改 `src/components/App.js` 为:

``` JavaScript
import React from 'react';
import './App.css';
import Header from './Header';

function App() {
	return (
		<div className="App">
			<Header text="movie app" />
		</div>
	);
}

export default App;

```

由于我们移动了 `App.js` 所以我们需要将 `src/index.js` 修改为:

``` JavaScript
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './components/App';	// 这行我们修改了引用路径
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

serviceWorker.unregister();

```  

在此项目中打开命令行,运行项目:  

``` terminal
npm start
```  

可以看到 
![初始化项目](https://user-gold-cdn.xitu.io/2019/11/12/16e6039365872cae?w=870&h=815&f=png&s=24191)

我们开始写部分样式,让 `Header` 组件看起来更header.  

``` css
/* src/components/App.css
 如果没有这个文件可以将 src/App.css 移入. */
.App {
	text-align: center;
}

.App-header {
	background-color: #282c34;
	height: 70px;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	font-size: calc(10px + 2vmin);
	color: white;
	padding: 20px;
	cursor: pointer;
}
```  

保存文件后我们就可以在 `localhost:3000` 中看到页面发生了变化.

## 3. Movie 组件

同样的,创建 `src/components/Movis.js` 文件:  

``` JavaScript
import React from 'react';

const DEFAULT_PLACEHOLDER_IMAGE = "https://m.media-amazon.com/images/M/MV5BMTczNTI2ODUwOF5BMl5BanBnXkFtZTcwMTU0NTIzMw@@._V1_SX300.jpg";

const Movie = ({ movie }) => {

	return (
		<div className="movie">
			<h2>银翼杀手</h2>

			<div>
				<img width="200" alt={`The movie titled: 银翼杀手`}
					src={DEFAULT_PLACEHOLDER_IMAGE} />
			</div>
			<p>2018-10-10</p>
		</div>
	);
};

export default Movie;
```  

只是简单的将影片信息展示出来,目前我们的影片信息都是固定的,后期会根据请求回来的数据进行修改,同时修改 `src/components/App.js`:  

``` JavaScript
import React from 'react';
import './App.css';
import Header from './Header';
import Movie from './Movie'

function App() {
	return (
		<div className="App">
			<Header text="movie app" />
			<Movie />
		</div>
	);
}

export default App;
```

JavaScript 书写的差不多了我们在将样式添加上去:

``` css
/* src/components/App.css*/
/* ... */
* {
	box-sizing: border-box;
}

@media screen and (min-width: 694px) and (max-width: 915px) {
	.movie {
	  max-width: 33%;
	}
  }
  
  @media screen and (min-width: 652px) and (max-width: 693px) {
	.movie {
	  max-width: 50%;
	}
  }
  
  
  @media screen and (max-width: 651px) {
	.movie {
	  max-width: 100%;
	  margin: auto;
	}
  }
```  

保存以上文件,我们可以看到 `localhost:3000` 发生了变化:
![添加 Movie 组件](https://user-gold-cdn.xitu.io/2019/11/12/16e6039781c29194?w=1041&h=603&f=png&s=148400)

## 4. 请求 movie 数据

现在先将我们的组件进程放在一边,先去请求 movie 数据,让页面看起来更真实.  

让我们去修改 `src/components/App.js`:

``` JavaScript
import React, { useState, useEffect } from 'react';
import './App.css';
import Header from './Header';
import Movie from './Movie'

const MOVIE_API_URL = "https://www.omdbapi.com/?s=man&apikey=5abd63d1";

function App() {
	const [loading, setLoading] = useState(true);
	const [movies, setMovies] = useState([]);
	const [errorMessage, setErrorMessage] = useState(null);

	useEffect(() => {
		fetch(MOVIE_API_URL)
			.then(response => response.json())
			.then(jsonResponse => {
				setMovies(jsonResponse.Search);
				setLoading(false);
			});
	}, []);

	return (
		<div className="App">
			<Header text="Movie app" />
			<div className="movies">
				{loading && !errorMessage ? (<span>loading...</span>) :
					errorMessage ? (<div className="errorMessage">{errorMessage}</div>) :
						(movies.map((movie, index) => (
							<Movie key={`${index}-${movie.Title}`} movie={movie} />
						)))}
			</div>
		</div>
	);
}

export default App;

```


同时,我们修改样式:  

``` css
/* App.css*/
/* ... */
.App-header h2 {
	margin: 0;
}
.movies {
	display: flex;
	flex-wrap: wrap;
	flex-direction: row;
}

.movie {
	padding: 5px 25px 10px 25px;
	max-width: 25%;
}
```

我们可以看到在某个循环内部我们调用了 `Movie` 组件,并传递了 `movie` 属性.我们来修改上一步在 `movie` 组件内部写死的数据:  

``` JavaScript
import React from 'react';

const DEFAULT_PLACEHOLDER_IMAGE = "https://m.media-amazon.com/images/M/MV5BMTczNTI2ODUwOF5BMl5BanBnXkFtZTcwMTU0NTIzMw@@._V1_SX300.jpg";

const Movie = ({ movie }) => {
	const poster = movie.Poster === "N/A" ? DEFAULT_PLACEHOLDER_IMAGE : movie.Poster;

	return (
		<div className="movie">
			<h2>{movie.Title}</h2>
			<div>
				<img width="200" alt={`The movie titled: ${movie.Title}`}
					src={poster} />
			</div>
			<p>({movie.Year})</p>

		</div>
	);
};

export default Movie;
```  

定义一个常量的意思是在电影(movie)没有海报时显示默认海报.  

现在我们刷新页面,可以看到新的电影信息已经请求完毕.  

![电影数据请求完毕](https://user-gold-cdn.xitu.io/2019/11/13/16e6548fa45bf092?w=1474&h=1027&f=png&s=1474464)

让我们回到 `App.js` 文件,相信你也看到了我们使用了一些新的 `hooks`:`useState`以及 `useEffect`. 这也是我们这个 demo 所重点展示的内容,先来看看官网对 `hooks`以及这两个新 `hooks` 的介绍:  

>Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。
>`useState` 通过在函数组件里调用它来给组件添加一些内部 state.`useState` 会返回一对值：当前状态和一个让你更新它的函数，你可以在事件处理函数中或其他一些地方调用这个函数。它类似 class 组件的 this.setState.
>`useEffect` Effect Hook 可以让你在函数组件中执行副作用操作.如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。

先来详细的说一下 `useState` :  
接受唯一的一个参数,为初始 state.在上面的例子中我们可以看到:  

``` JavaScript
	const [loading, setLoading] = useState(true);
	const [movies, setMovies] = useState([]);
```  

我们在一个函数组件中调用了两次 `useState` hook,第一次相当于我们定义了一个 loading state ,并将它的初始值设置为 `true`(`useState`的参数),并定义了更新此state 的 `setLoading` 方法.第二次同理. 现在我们使用等价的 class 示例来展示 `useState` hook的使用:  

``` JavaScript
// App.js
import React, { useState, useEffect } from 'react';
import './App.css';
import Header from './Header';
import Movie from './Movie'

const MOVIE_API_URL = "https://www.omdbapi.com/?s=man&apikey=5abd63d1";

class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            loading: true,
            movies: [],
            errorMessage: null
        }
    }
    componentDidMount() {
        fetch(MOVIE_API_URL)
            .then(response => response.json())
            .then(jsonResponse => {
            this.setState({
                movies: jsonResponse.Search,
                loading: false
            })
        });
    }
    render() {
        return (
            <div className="App" >
                <Header text="Movie App" />
                <div className="movies">
                	{this.state.loading && !this.state.errorMessage ?
            		(<span>loading</span>) :
            		this.state.errorMessage ?
            			(<div className="errorMessage">
            				{this.state.errorMessage}
            			</div>) : (this.state.movies.map((movie, index) => (
            				<Movie key={`${index}-${movie.Title}`}
            					movie={movie} />
                	)))}
                </div>
            </div>
        )
    }

}
export default App;

```  

先主要看  `constructor` 函数中我们通过 `this.state` 定义了三个`state`: `loading`:负责页面的加载状态,`movies`用于存储请求来的电影数据,`errorMessage`:保存请求失败后的一些错误信息.并赋予了初始值.而后在 `componentDidMount` 声明周期函数中我们请求数据,并使用 `this.setState` 修改了我们定义的某些 `state`.  

通过两者的比较,我们发现 `useState` hook 大大精简了我们设置 `state` 以及更新 `state` 的方式,而 `useEffect` 则是精简了我们在生命周期函数中重述书写某些逻辑的语句.    


## 5. Search 组件

让我们为此 demo 添加 `Search` 组件:  

``` javascript
// components/Search.js
import React, { useState } from 'react';

const Search = (props) => {
	const [searchValue, setSearchValue] = useState("")

	const handleSearchInputChanges = (e) => {
		setSearchValue(e.target.value);
	}
	const resetInputField = () => {
		setSearchValue("")
	}
	const callSearchFunction = () => {
		e.preventDefault();
		props.search(searchValue);
		resetInputField();
	}
	return (
		<form className="search">
			<input
				value={searchValue}
				onChange={handleSearchInputChanges}
				type="text"
			/>
			<input type="submit" onClick={callSearchFunction} value="SEARCH" />
		</form>
	)
}

export default Search;

```  

同时,修改 css 样式:  

``` css

/* App.css */
.search {
	display: flex;
	flex-direction: row;
	flex-wrap: wrap;
	justify-content: center;
	margin-top: 10px;
}


input[type="submit"] {
	padding: 5px;
	background-color: transparent;
	color: black;
	border: 1px solid black;
	width: 80px;
	margin-left: 5px;
	cursor: pointer;
}


input[type="submit"]:hover {
	background-color: #282c34;
	color: antiquewhite;
}


.search>input[type="text"] {
	width: 40%;
	min-width: 170px;
}
```  

为 `App.js` 添加相关逻辑:  

``` JavaScript
// App.js
import React, { useState, useEffect } from 'react';
import './App.css';
import Header from './Header';
import Movie from './Movie';
import Search from './Search';	// new
const MOVIE_API_URL = "https://www.omdbapi.com/?s=jackie&apikey=5abd63d1";

function App() {
	const [loading, setLoading] = useState(true);
	const [movies, setMovies] = useState([]);
	const [errorMessage, setErrorMessage] = useState(null);

	useEffect(() => {
		fetch(MOVIE_API_URL)
			.then(response => response.json())
			.then(jsonResponse => {
				setMovies(jsonResponse.Search);
				setLoading(false);
			});
	}, []);

	// new function
	const search = searchValue => {
		setLoading(true);
		setErrorMessage(null);
		fetch(`https://www.omdbapi.com/?s=${searchValue}&apikey=5abd63d1`)
			.then(res => res.json())
			.then(jsonRes => {
				if (jsonRes.Response === "True") {
					setMovies(jsonRes.Search);
				} else {
					setErrorMessage(jsonRes.Error);
				}
				setLoading(false)
			})
	}
	return (
		<div className="App">
			<Header text="Movie App" />
			<Search search={search} />
			<p className="App-intro">分享快乐,从电影开始</p>
			<div className="movies">
				{loading && !errorMessage ? (<span>loading...</span>) :
					errorMessage ? (<div className="errorMessage">{errorMessage}</div>) :
						(movies.map((movie, index) => (
							<Movie key={`${index}-${movie.Title}`} movie={movie} />
						)))}
			</div>
		</div>
	);
}

export default App;

```  

包含 css 的调整,可以直接看 [源码地址](https://user-gold-cdn.xitu.io/2019/11/16/16e74d7bd1def9f4) .  

这里主要说一下添加的一些东西.主要是 `Search` 组件的添加,以及点击 `SEAECH` 按钮时发送请求的逻辑,我们将逻辑请求放在的父组件中来做,这是基本的组件设计原则.因为另外一个子组件 `Movie` 需要用到我们请求返回的数据.  

同样的,我们附上 `class component`版本的`App.js`:  

``` JavaScript
import React from 'react';
import './App.css';
import Header from './Header';
import Movie from './Movie'
import Search from "./Search";
const MOVIE_API_URL = "https://www.omdbapi.com/?s=man&apikey=5abd63d1";

class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
			loading: true,
			movies: [],
			errorMessage: null
		}
		this.search = this.search.bind(this);
	}
	search(searchValue) {
		console.log(this)
		this.setState({
			loading: true,
			errorMessage: null
		})
		fetch(`https://www.omdbapi.com/?s=${searchValue}&apikey=5abd63d1`)
			.then(res => res.json())
			.then(jsonRes => {
				if (jsonRes.Response === "True") {
					this.setState({
						loading: false,
						movies: jsonRes.Search
					})
				} else {
					this.setState({
						loading: false,
						errorMessage: jsonRes.Error
					})
				}
			})
	}
	componentDidMount() {
		fetch(MOVIE_API_URL)
			.then(response => response.json())
			.then(jsonResponse => {
				this.setState({
					movies: jsonResponse.Search,
					loading: false
				})
			});
	}
	render() {
		return (
			<div className="App" >
				<Header text="Movie App" />
				<Search search={this.search} />
				<div className="movies">
					{this.state.loading && !this.state.errorMessage ?
						(<span>loading</span>) :
						this.state.errorMessage ?
							(<div className="errorMessage">
								{this.state.errorMessage}
							</div>) : (this.state.movies.map((movie, index) => (
								<Movie key={`${index}-${movie.Title}`}
									movie={movie} />
							)))}
				</div>
			</div>
		)
	}

}
export default App;

```

最大的不同也是事件处理需要绑定 `this`.  

到这里应该说一个基本的 `hooks` 演示demo已经结束了,但是我们想更近一步,使用 `useReducer` 来替换 `useState` ,我们先来看看官方对 `useReducer`的定义:  
> `const [state, dispatch] = useReducer(reducer, initialArg, init);`
> useState 的替代方案。它接收一个形如 (state, action) => newState 的 reducer，并返回当前的 state 以及与其配套的 dispatch 方法。（如果你熟悉 Redux 的话，就已经知道它如何工作了。）

> 在某些场景下，useReducer 会比 useState 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。并且，使用 useReducer 还能给那些会触发深更新的组件做性能优化，因为你可以向子组件传递 dispatch 而不是回调函数 。  

具体到本项目中的使用呢,就是:  

``` JavaScript
// App.js
import React, { useReducer, useEffect } from 'react';
import './App.css';
import Header from './Header';
import Movie from './Movie';
import Search from './Search';
const MOVIE_API_URL = "https://www.omdbapi.com/?s=jackie&apikey=5abd63d1";
const initialState = {
	loading: true,
	movies: [],
	errorMessage: null
}
function reducer(state, action) {
	switch (action.type) {
		case 'requesting':
			return {
				...state,
				loading: true
			};
		case 'query_success':
			return {
				...state,
				loading: false,
				movies: action.movies
			};
		case 'query_error':
			return {
				...state,
				loading: false,
				errorMessage: action.error
			};
		default:
			return state;
	}
}
function App() {

	const [state, dispatch] = useReducer(reducer, initialState);

	useEffect(() => {
		fetch(MOVIE_API_URL)
			.then(response => response.json())
			.then(jsonResponse => {
				dispatch({
					type: "query_success",
					movies: jsonResponse.Search
				})
			});
	}, []);

	const search = searchValue => {
		dispatch({
			type: "requesting"
		})
		fetch(`https://www.omdbapi.com/?s=${searchValue}&apikey=5abd63d1`)
			.then(res => res.json())
			.then(jsonRes => {
				if (jsonRes.Response === "True") {
					dispatch({
						type: "query_success",
						movies: jsonRes.Search
					})
				} else {
					dispatch({
						type: "query_error",
						error: jsonRes.Error
					})
				}
			})
	}
	const { movies, loading, errorMessage } = state;
	return (
		<div className="App">
			<Header text="Movie App" />
			<Search search={search} />
			<p className="App-intro">分享快乐,从电影开始</p>
			<div className="movies">
				{loading && !errorMessage ? (<span>loading...</span>) :
					errorMessage ? (<div className="errorMessage">{errorMessage}</div>) :
						(movies.map((movie, index) => (
							<Movie key={`${index}-${movie.Title}`} movie={movie} />
						)))}
			</div>
		</div>
	);
}

export default App;
```  

对比使用 `useState` 和 `useReducer` 来看,逻辑会更加的清晰,还会有一些性能上的优化.  

## 6. 总结

基本上这个小小的 demo 到这里就结束了.  
主要就是一些简单 hooks 的使用以及与 `class component`使用的一些代码上的区别.更多的 hooks 以及自定义 hooks 没有涉及到,这个在 [官方文档](https://user-gold-cdn.xitu.io/2019/11/16/16e74d7c16ea00d2)  写的很详细,建议大家阅读.这里只是借花献佛的"抛砖引玉"一下.




