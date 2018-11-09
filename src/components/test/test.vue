<template>
    <div id="magnifier">
        <div class="small-box">
            <img src="images/small_1.jpg" alt="#">
            <span class="hover"></span>
        </div>
        <div class="thumbnail-box">
            <a href="javascript:;" class="btn btn-prev"></a>
            <a href="javascript:;" class="btn btn-next"></a>
            <div class="list">
                <ul class="wrapper">
                    <li v-for="item in gooddata" :key="item.id" class="item " :data-src="item.thumb_path"><img :src="item.thumb_path" alt="#"></li>
                    <!-- <li class="item" data-src="images/small_2.jpg"><img src="images/thumbnail_2.jpg" alt="#"></li>
                    <li class="item" data-src="images/small_3.jpg"><img src="images/thumbnail_3.jpg" alt="#"></li>
                    <li class="item" data-src="images/small_4.jpg"><img src="images/thumbnail_4.jpg" alt="#"></li>
                    <li class="item" data-src="images/small_5.jpg"><img src="images/thumbnail_5.jpg" alt="#"></li>
                    <li class="item" data-src="images/small_6.jpg"><img src="images/thumbnail_6.jpg" alt="#"></li> -->
                </ul>
            </div>
        </div>
        <div class="big-box">
            <img src="images/big_1.jpg" alt="#">
        </div>
    </div>
</template>

<style scoped>
    * {
        margin: 0;
        padding: 0;
    }

    ul,
    li {
        list-style: none;
    }

    #magnifier {
        position: relative;
        width: 450px;
        margin: 54px 0 0 70px;
    }

    .small-box {
        position: relative;
        width: 450px;
        height: 450px;
        margin-bottom: 20px;
        border: 1px solid #eee;
    }

    .small-box img {
        display: block;
    }

    .small-box .hover {
        display: none;
        position: absolute;
        left: 0;
        top: 0;
        width: 200px;
        height: 200px;
        border: 1px solid #aaa;
        background: #0099ff;
        opacity: .5;
        /* filter: alpha(opacity: 50); */
        cursor: move;
    }

    .thumbnail-box {
        position: relative;
        width: 100%;
    }

    .thumbnail-box .btn {
        position: absolute;
        top: 50%;
        width: 22px;
        height: 32px;
        margin-top: -16px;
    }

    .thumbnail-box .btn-prev {
        left: 0;
        background: url() no-repeat;
    }

    .thumbnail-box .btn-next {
        right: 0;
        background: url() no-repeat;
    }

    .thumbnail-box .list {
        overflow: hidden;
        width: 390px;
        margin: 0 auto;
    }

    .thumbnail-box .wrapper {
        width: 100000px;
    }

    .thumbnail-box .list .item {
        float: left;
        margin: 0 10px;
    }

    .thumbnail-box .list .item-cur {}

    .thumbnail-box .list .item img {
        border: 2px solid #fff;
    }

    .thumbnail-box .list .item-cur img {
        border: 2px solid #e53e41;
    }

    .big-box {
        display: none;
        overflow: hidden;
        position: absolute;
        left: 451px;
        top: 0;
        width: 540px;
        height: 540px;
        border: 1px solid #e4e4e4;
    }

    .big-box img {
        display: block;
    }
</style>

<script>
    import '../../statics/site/jquery_plugins/test.js'
    export default {
        data(){
            return{
                gooddata:[]
            }
        },
        created() {
            this.getlist()
        },

        mounted() {

            var Magnifier = function (elem) {
                var self = this;
                this.$elem = elem;
                this.$smallBox = this.$elem.find('.small-box');
                this.$smallBox_pic = this.$smallBox.find('img');
                this.$smallBox_mask = this.$smallBox.find('.hover');
                this.$thumbnailBox = this.$elem.find('.thumbnail-box');
                this.$thumbnailBox_prev = this.$thumbnailBox.find('.btn-prev');
                this.$thumbnailBox_next = this.$thumbnailBox.find('.btn-next');
                this.$thumbnailBox_wrapper = this.$thumbnailBox.find('.wrapper');
                this.$thumbnailBox_item = this.$thumbnailBox.find('.item');
                this.$thumbnailBox_pic = this.$thumbnailBox.find('img');
                this.$bigBox = this.$elem.find('.big-box');
                this.$bigBox_pic = this.$bigBox.find('img');
            };
            Magnifier.prototype = {
                moveBigPic: function () {
                    var scaleX = this.$smallBox_mask.position().left / (this.$smallBox.width() - this.$smallBox_mask
                        .width());
                    var scaleY = this.$smallBox_mask.position().top / (this.$smallBox.height() - this.$smallBox_mask
                        .height());
                    var scroll_l = scaleX * (this.$bigBox_pic.width() - this.$bigBox.width());
                    var scroll_t = scaleY * (this.$bigBox_pic.height() - this.$bigBox.height());
                    this.$bigBox.scrollLeft(scroll_l).scrollTop(scroll_t);
                },
                changeSrouce: function (index, cur_src) {
                    this.$smallBox_pic.attr('src', cur_src);
                    this.$bigBox_pic.attr('src', 'images/big_' + (index + 1) + '.jpg');
                },
                setMask: function () {
                    var mask_w = this.$smallBox.width() / (this.$bigBox_pic.width() / this.$bigBox.width());
                    var mask_h = this.$smallBox.height() / (this.$bigBox_pic.height() / this.$bigBox.height());
                    this.$smallBox_mask.css({
                        width: mask_w,
                        height: mask_h
                    });
                },
                inital: function () {
                    var self = this;
                    this.$thumbnailBox_next.click(function () {
                        var ov_pic = self.$thumbnailBox_item.length - 5;
                        var ov_dis = ov_pic * 78;
                        if (ov_pic > 0) {
                            self.$thumbnailBox_wrapper.animate({
                                marginLeft: -ov_dis
                            });
                        }
                    });
                    this.$thumbnailBox_prev.click(function () {
                        self.$thumbnailBox_wrapper.animate({
                            marginLeft: 0
                        });
                    });
                    this.$thumbnailBox_item.mouseover(function () {
                        var cur_src = $(this).attr('data-src');
                        self.$thumbnailBox_item.removeClass('item-cur');
                        $(this).addClass('item-cur');
                        self.changeSrouce($(this).index(), cur_src);
                    });
                    this.$smallBox.hover(function () {
                        self.$bigBox.show();
                        self.$smallBox_mask.show();
                        self.setMask();
                        $(this).mousemove(function (ev) {
                            var oEvent = ev || window.event;
                            var offset_pos = {
                                left: oEvent.clientX - $(this).offset().left - self
                                    .$smallBox_mask.width() / 2,
                                top: oEvent.clientY - $(this).offset().top - self.$smallBox_mask
                                    .height() / 2 + $(window).scrollTop()
                            };
                            if (offset_pos.left < 0) {
                                offset_pos.left = 0;
                            } else if (offset_pos.left > $(this).width() - self.$smallBox_mask
                                .width()) {
                                offset_pos.left = $(this).width() - self.$smallBox_mask
                                    .width();
                            }
                            if (offset_pos.top < 0) {
                                offset_pos.top = 0;
                            } else if (offset_pos.top > $(this).height() - self.$smallBox_mask
                                .height()) {
                                offset_pos.top = $(this).height() - self.$smallBox_mask
                                    .height();
                            }
                            self.$smallBox_mask.css(offset_pos);
                            self.moveBigPic();
                        });
                    }, function () {
                        self.$smallBox_mask.hide();
                        self.$bigBox.hide();
                    });
                },
                constructor: Magnifier
            };
            $.fn.magnifier = function () {
                var magnifier = new Magnifier(this);
                return magnifier.inital();
            };

        },
        methods:{
            getlist(){
            const url ='site/goods/getgoodsinfo/87'
            this.$axios.get(url).then(responst=>{
                this.gooddata= responst.data.message.imglist
                console.log(this.gooddata)
            })
        }
        }

     

    }
</script>