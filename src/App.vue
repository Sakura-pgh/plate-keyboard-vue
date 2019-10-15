<template>
	<div id="app">
		<!-- <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div> -->
		<router-view />
	</div>
</template>

<style lang="scss">
#app {
	font-family: "Avenir", Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}
#nav {
	padding: 30px;
	a {
		font-weight: bold;
		color: #2c3e50;
		&.router-link-exact-active {
			color: #42b983;
		}
	}
}

html {
	border: 0;
	outline: 0;
	vertical-align: baseline;
	background: transparent;
	margin: 0;
	padding: 0;
}

/*样式清零*/

body,
div,
span,
applet,
object,
iframe,
h1,
h2,
h3,
h4,
h5,
h6,
p,
blockquote,
pre,
a,
abbr,
acronym,
address,
big,
cite,
code,
del,
dfn,
em,
font,
img,
ins,
kbd,
q,
s,
samp,
small,
strike,
strong,
section,
aside,
article,
sub,
sup,
tt,
var,
b,
u,
i,
center,
dl,
dt,
dd,
ol,
ul,
li,
fieldset,
form,
label,
legend,
table,
caption,
tbody,
tfoot,
thead,
tr,
th,
td {
	border: 0;
	outline: 0;
	vertical-align: baseline;
	background: transparent;
	margin: 0;
	padding: 0;
	font-style: normal;
}

ol,
ul {
	list-style: none;
}

blockquote,
q {
	quotes: none;
}

blockquote {
	&:before,
	&:after {
		content: none;
	}
}

q {
	&:before,
	&:after {
		content: none;
	}
}

:focus {
	outline: 0;
}

ins {
	text-decoration: none;
}

del {
	text-decoration: line-through;
}

table {
	border-collapse: collapse;
	border-spacing: 0;
}

article,
section {
	display: block;
}

body {
	font-family: "PingFang SC", "STHeitiSC-Light", "Helvetica-Light", arial,
		sans-serif, "Droid Sans Fallback";
	font-size: 14px;
	font-weight: normal;
	line-height: 1.42;
	color: #333333;
}

/*样式清零END*/
</style>
