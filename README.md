# canvas-make-progress
canvas画圆形进度条
> 首先上代码

    class Pie {
        constructor({el, ratio = 1, deltaAngle = 0, lineWidth = 4, lineColor = '#FF6A82', step = 0.1} = {}) {
            let a = $('canvas');
            if (a.length !== 0){
                $('canvas').remove();
            }
    
            if (!el || typeof el !== 'string') {
                throw new Error('请传入一个元素，字符串')
            }
            this.adaptSize = (size) => {
                return size * ratio
            };
            this.adaptAngle = (angle) => {
                // 单位是PI 弧度
                return angle + (deltaAngle)
            };
            
    
            this.el = document.querySelector(el);
            this.canvas = document.createElement('canvas');
    
    
            let {width, height} = this.el.getBoundingClientRect();
            // this.canvas.width = width;
            // this.canvas.height = height;
    
            this.dpr = window.devicePixelRatio || 1;
    
            this.canvas.style.width = width + 'px';
            this.canvas.style.height = height + 'px';
            this.canvas.height = height * this.dpr;
            this.canvas.width = width * this.dpr;
    
            this.ctx = this.canvas.getContext('2d');
    
            this.el.appendChild(this.canvas);
            this.step = step * this.dpr;
            this.ctx.lineWidth = this.adaptSize(lineWidth) * this.dpr;
            // this.ctx.lineJoin = 'round';
            // this.ctx.lineCap = 'round';
            this.R = [width / 2 * this.dpr, height / 2 * this.dpr]; // 圆心
            this.r = (this.R[0] - this.ctx.lineWidth / 2); // 圆弧半径
            this.ctx.strokeStyle = lineColor;
    
            this.img = this.el.querySelector('img');
        }
    
        to(angle) {
            let start = this.adaptAngle(0);
            let end = this.adaptAngle(angle);
            let cur = start;
            const run = () => {
                if (cur < end) {
                    cur += this.step;
                    this.ctx.clearRect(0, 0, this.R[0], this.R[1]);
                    this.ctx.beginPath();
                    this.ctx.arc(this.R[0], this.R[1], this.r, start, cur);
                    // this.ctx.scale(0.5, 0.5);
                    this.ctx.stroke();
                    this.ctx.closePath();
                    requestAnimationFrame(run);
                }
                this.drawItem(cur);
            };
            run()
        }
        drawItem(cur) {
            let y = this.R[0] / this.dpr * Math.sin(cur);
            let x = this.R[1] / this.dpr * Math.cos(cur);
            this.img.style.transform = `translate(${x}px, ${y}px)`;
            this.img.style.webkitTransform = `translate(${x}px, ${y}px)`;
        }
    }
    renderCircle() {
        this.pie = new Pie({
            el: '.pie',
            ratio: $(window).width() / 750,
            deltaAngle: Math.PI * -1,
            lineColor: '#FF6A82'
        });
        setTimeout(() => {
            if (this.headInfo.process_percent === 0){
                this.headInfo.process_percent = 0.01 //防止出现小蓝块在圆形中心的情况
            }
            this.pie.to(2 * Math.PI * this.headInfo.process_percent / 100)
        })
    }
    
    
动态创建canvas节点是因为可以组件化，方便拿到其他地方使用。

