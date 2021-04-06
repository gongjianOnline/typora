# grid布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            box-sizing: border-box;
            margin: 0px;
            padding: 0px;
        }
        .container{
            height: 100vh;
            border: 1px solid red;
            display: grid;
            grid-row-gap: 10px;
            grid-column-gap:10px;
            grid-template-rows: 40px auto 40px;
            grid-template-columns: 200px auto;
            grid-template-areas:
            "header header"
            "nav main"
            "footer footer";
        }
        .container > header{
            grid-area: header;
            border: 1px solid rebeccapurple;
        }
        .container > nav{
            grid-area: nav;
            border: 1px solid rebeccapurple;
        }
        .container > main{
            grid-area: main;
            border: 1px solid rebeccapurple;
        }
        .container > footer{
            grid-area: footer;
            border: 1px solid rebeccapurple;
        }
    </style>
</head>
<body>
    <div class="container">   
        <header>header</header>
        <nav>nav</nav>
        <main>main</main>
        <footer>footer</footer>
    </div>
</body>
</html>
```

