<!DOCTYPE html>
<html>
<head lang="zh-cn">
    <meta charset="UTF-8">
    <title></title>
</head>
<style>
    body {
        width:640px;
        margin:20px 30px;
    }

    #status {
        width:10px;
        height: 10px;
        border-radius: 50%;
        position: absolute;
        top:15px;
        left: 0;
        margin:10px;
        background: #999;
    }

    #status.working {
        -webkit-animation: flash 1s infinite;
        -webkit-animation-direction: alternate;
    }

    #msg {
        max-height: 300px;
        overflow: auto;
        border-radius: 5px;
        border: 1px solid #fff;
        margin: 20px 0;
        padding: 5px 15px;
        background: linear-gradient(to top,#00ff72,#57a9ffab);
    }

    #msg li {
        margin:5px 20px;
    }

    @-webkit-keyframes  flash {
        from {
            box-shadow: 0 0 5px green;
            background: green;
        }

        to {
            box-shadow: 0 0 12px green;
            background: green;
        }
     }
     #switch,#clean,#test{
        display: inline-block;
        margin-bottom: 0;
        font-weight: 400;
        text-align: center;
        -ms-touch-action: manipulation;
        touch-action: manipulation;
        cursor: pointer;
        background-image: none;
        border: 1px solid transparent;
        white-space: nowrap;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        padding: 5px 15px 6px;
        font-size: 12px;
        border-radius: 4px;
        transition: color .2s linear,background-color .2s linear,border .2s linear,box-shadow .2s linear;
        color: #fff;
        background-color: #2d8cf0;
        border-color: #2d8cf0;
     }
</style>
<body>

<div id="status"></div>
<button id="switch">开启</button>
<button id="clean">清空</button>
<button id="test">javascript</button>
<button id="test">nodejs</button>
<button id="test">webpack</button>
<button id="test">commonjs</button>

<ol id="msg" title="性能测试"></ol>


<script>
    var Btn = function () {
        this._switch = document.querySelector('#switch');
        this._clean  = document.querySelector('#clean');
        this._msg    = document.querySelector('#msg');
        this._status = document.querySelector('#status');
        this.es = null;

        this.init();
    };
    


    Btn.prototype.init = function () {
        var that = this;

        var _msg = that._msg;
        that._clean.addEventListener('click', function () {
            _msg.innerHTML = '';
        });

        that._switch.addEventListener('click', function () {
            if(this.innerText === '开启') {
                that.on();
            } else {
                that.off();
            }
        });

        that.on();
    };

    Btn.prototype.on = function () {
        var that = this;

        // 1. 声明EventSource
        that.es = new EventSource('http://15.push1.eastmoney.com/sse?cname=TSQ_SZ002875');
        // 2. 监听数据
        that.es.onmessage = function (e) {
            var newdata=eval('(' + e.data + ')');
            
            if(newdata){
                let toDayNum=0;
                if(eval('(' + newdata.seq + ')')&&eval('(' + newdata.seq + ')')=='807450'){
                    if(eval('(' + newdata.content + ')')[31]){
                        toDayNum=eval('(' + newdata.content + ')')[31];
                    }
                }
                if(newdata.content){
                    if(eval('(' + newdata.content + ')')[43]){
                    
                        document.querySelector('#msg').innerHTML += '<pre>testDemo:'+ eval('(' + newdata.content + ')')[43] +'毫秒   time:'+timestampToTime("Y-m-d H:i:s",eval('(' + newdata.content + ')')[86])+'</pre>';
                        
                        if(eval('(' + newdata.content + ')')[71]&&eval('(' + newdata.content + ')')[71]!=0){
                            document.querySelector('#msg').innerHTML +='<pre>javascript:'+(((eval('(' + newdata.content + ')')[43]-eval('(' + newdata.content + ')')[31])/eval('(' + newdata.content + ')')[31])*100).toFixed(2)+'</pre>';
                        }
                    }
                }
                
            }
           
            
        };

        that._switch.innerText = '关掉';
        that._status.classList.add('working');
    };

    Btn.prototype.off = function () {
        this.es.close();

        this._switch.innerText = '开启';
        this._status.classList.remove('working');
    };

    new Btn();
</script>
</body>
</html>
