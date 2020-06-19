<template>
    <div class="about">
        <h1>This is an about page</h1>
        <div class="container">
            <div tabindex="0" :class="{'is-dragover': dragover}" class="el-upload-dragger" @drop.prevent="onDrop"
                @dragover.prevent="onDragover" @dragleave.prevent="onDropLeave" @dragenter.prevent="onDropEnter">
                <div class="upload-inner">
                    <p>将文件拖到此处上传</p>
                    <span class="add-icon">+</span>
                </div>
            </div>
            <div id="imgBox" class="img-box">
                照片展示区
            </div>
        </div>
        <p>{{fileName}}</p>
        <button @click="enlarge" v-if="fileName">放大</button>
        <button @click="delarge" v-if="fileName">缩小</button>
    </div>
</template>
<script>
export default {
    data() {
        return {
            dragover: false,
            files: [],
            fileName: ''
        };
    },
    methods: {
        onDrop(e) {
            console.log(e);
            e.stopPropagation();
            const imgBox = document.getElementById('imgBox');
            imgBox.innerHTML = '加载中...';
            imgBox.style.width = '200px';
            this.dragover = false;
            this.files = e.dataTransfer.files;
            this.fileName = this.files[0].name;
            const img = document.createElement('img');
            img.style.width = '100%';
            const reader = new FileReader();
            reader.onload = function (ev) {
                console.log(ev);
                img.src = this.result;
                imgBox.appendChild(img);
            }
            reader.onprogress = function (ev) {
                console.log(ev);
                const percentComplete = Math.round(ev.loaded * 100 / ev.total);
                if (ev.loaded === ev.total) {
                    imgBox.innerHTML = '';
                }
                console.log(percentComplete);
            }
            reader.readAsDataURL(this.files[0]);
        },
        onDragover() {
            this.dragover = true;
            this.fileName = '';
            // console.log('onDragover');
        },
        onDropEnter() {
            // console.log('onDropEnter');
        },
        onDropLeave() {
            this.dragover = false;
            console.log('onDropLeave');
            document.getElementById('imgBox').innerHTML = '';
        },
        changeSize(direction = 1) {
            const imgBox = document.getElementById('imgBox');
            if (imgBox) {
                const imgBoxWidth = imgBox.getBoundingClientRect().width;
                imgBox.style.width = (1 + direction * 0.1) * imgBoxWidth + 'px';
            }
        },
        enlarge() {
            this.changeSize();
        },
        delarge() {
            this.changeSize(-1);
        }
    },
    beforeRouteLeave(to, from, next) {
        if (window.confirm('您确定要离开吗？')) {
            next();
            
        } else {
            next(false);
        }
    }

}

</script>
<style scoped lang="less">
    .container {
        display: flex;
        margin-left: 2rem;
    }

    .el-upload-dragger {
        background-color: #fff;
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        box-sizing: border-box;
        width: 260px;
        height: 180px;
        text-align: center;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-right: 2rem;

        .add-icon {
            font-size: 4rem;
        }

        &:hover {
            border-color: #409eff;
            .upload-inner {
                color: #409eff;
            }
        }

        &.is-dragover {
            background-color: rgba(32, 159, 255, .06);
            border: 2px dashed #409eff;
            .upload-inner {
                color: #409eff;
            }
        }
    }

    .img-box {
        width: 200px;
        min-height: 50px;
        background-color: #ccc;
        border-radius: 5px;
        padding: 10px;
        display: flex;
        align-items: center;
        justify-content: center;
    }
</style>
