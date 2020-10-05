<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->
<!-- This is your HTML -->
<template>
    <div class="slider-section">
        <!-- wwManager:start -->
        <wwSectionEditMenu :sectionCtrl="sectionCtrl"></wwSectionEditMenu>
        <!-- wwManager:end -->

        <!-- This is the background of the section -->
        <wwObject class="background" :ww-object="section.data.background" ww-category="background"> </wwObject>

        <div class="content-container">
            <!-- This is a simple WeWeb object which can be anything in the editor -->

            <!-- This is another WeWeb object that we initiaze as an image -->
            <div class="left-container">
                <div v-for="(item, index) in section.data.contentList" :key="index" class="header" :class="[slideIndex == index ? 'show ' : '']">
                    <wwObject class="title" :ww-object="item.title"></wwObject>
                    <wwObject class="subtitle" :ww-object="item.subtitle"></wwObject>
                </div>
                <div class="icons-list">
                    <div class="points">
                        <wwObject v-for="(item, index) in section.data.contentList" :key="index" :class="[slideIndex == index ? 'current' : '', 'dot icon-' + index]" :ww-object="item.icon" v-on:click="showSlide(index)"></wwObject>
                    </div>
                    <wwObject class="icon" :ww-object="section.data.iconLeft" v-on:click="previousSlide"></wwObject>
                    <wwObject class="icon" :ww-object="section.data.iconRight" v-on:click="nextSlide"></wwObject>
                </div>
            </div>
            <!-- This is a content block, a list of objects, it will add the little blue "plus" around the objects in the editor -->
            <div class="right-container">
                <wwObject
                    v-for="(item, index) in section.data.contentList"
                    :key="index"
                    ww-default-ratio="80"
                    class="slider-image"
                    :class="[slideIndex >= index ? 'active' : '', item.mainImage ? 'main-image' : '']"
                    :ww-object="item.image"
                ></wwObject>
            </div>
            <!-- wwManager:start -->
            <div class="edit-custom-block" v-if="sectionCtrl.getEditMode() == 'CONTENT'">
                <div class="icon-edit" v-on:click="removeSlide(null)">
                    <font-awesome-icon class="awesome-icon" icon="trash" />
                </div>
                <div class="icon-edit" v-on:mousedown="addSlide">
                    <font-awesome-icon class="awesome-icon" icon="plus" />
                </div>
            </div>
            <!-- wwManager:end -->
        </div>
        <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.bottomList" @ww-add="add(section.data.bottomList, $event)" @ww-remove="remove(section.data.bottomList, $event)">
            <wwObject tag="div" v-for="object in section.data.bottomList" :key="object.uniqueId" :ww-object="object"></wwObject>
        </wwLayoutColumn>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
import Vue from 'vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faPlus, faTrash } from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

library.add(faTrash, faPlus);

Vue.component('font-awesome-icon', FontAwesomeIcon);

export default {
    name: '__COMPONENT_NAME__',
    props: {
        // The section controller object is passed. You can access it anytime
        sectionCtrl: Object
    },
    data() {
        return {
            slideIndex: 0,
            timer: null
        };
    },
    computed: {
        // The section object contains all the info and data about the section
        // Use it as you like !
        section() {
            return this.sectionCtrl.get();
        }
    },
    created() {
        // Initialize the data once the section is created in the DOM
        this.init();
    },
    mounted() {
        this.startTimer();
    },
    methods: {
        init() {
            // Initialize section data
            let needUpdate = false;

            // We will only save the data in this.section.data
            // So you need to put in there all the data of you WeWeb objects
            this.section.data = this.section.data || {};

            // Initialize WeWeb objects that are in the html template up there

            if (!this.section.data.background) {
                this.section.data.background = wwLib.wwObject.getDefault({ type: 'ww-color' });
                needUpdate = true;
            }

            if (!this.section.data.iconLeft) {
                this.section.data.iconLeft = wwLib.wwObject.getDefault({
                    type: 'ww-icon',
                    data: {
                        icon: 'fas fa-angle-left',
                        style: {
                            size: 32
                        }
                    }
                });
                needUpdate = true;
            }

            if (!this.section.data.iconRight) {
                this.section.data.iconRight = wwLib.wwObject.getDefault({
                    type: 'ww-icon',
                    data: {
                        icon: 'fas fa-angle-right',
                        style: {
                            size: 32,
                            paddingLeft: 20
                        }
                    }
                });
                needUpdate = true;
            }

            if (!this.section.data.contentList) {
                this.section.data.contentList = [
                    {
                        image: wwLib.wwObject.getDefault({
                            type: 'ww-image',
                            data: {
                                url: 'https://weweb.twic.pics/designs/436/sections/Group_12.png?%27}twic=v1/quality=85/resize=1024',
                                zoom: 0.87,
                                style: {
                                    // minWidth: 640,
                                    ratio: 1
                                }
                            }
                        }),
                        icon: wwLib.wwObject.getDefault({
                            type: 'ww-icon',
                            data: {
                                icon: 'fas fa-circle',
                                style: {
                                    size: 12,
                                    fontSize: 12,
                                    borderWidth: 0
                                }
                            }
                        }),
                        title: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Peers amongst entrepreneurs'
                                }
                            }
                        }),
                        subtitle: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: ''
                                }
                            }
                        }),
                        mainImage: true,
                        isActive: true
                    },
                    {
                        image: wwLib.wwObject.getDefault({
                            type: 'ww-image',
                            data: {
                                url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_8.png?%27}twic=v1/quality=85/resize=1024',
                                zoom: 0.63,
                                position: { x: -7.1, y: 11.4 },
                                style: {
                                    // minWidth: 510,
                                    ratio: 1,
                                    borderRadius: 100
                                }
                            }
                        }),
                        icon: wwLib.wwObject.getDefault({
                            type: 'ww-icon',
                            data: {
                                icon: 'fas fa-circle',
                                style: {
                                    size: 12,
                                    fontSize: 12,
                                    color: '#343C38',
                                    borderWidth: 0
                                }
                            }
                        }),
                        title: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Thomas’ strategic perspective on product roadmap anbd Alex’ support on go-to-market made the difference'
                                }
                            }
                        }),
                        subtitle: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Founder Adverity'
                                }
                            }
                        }),
                        isActive: false
                    },
                    {
                        image: wwLib.wwObject.getDefault({
                            type: 'ww-image',
                            data: {
                                url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_5.png?%27}twic=v1/quality=85/resize=1024',
                                zoom: 0.63,
                                position: { x: -19.1, y: -0.9 },
                                style: {
                                    // minWidth: 510,
                                    ratio: 1,
                                    borderRadius: 100
                                }
                            }
                        }),
                        icon: wwLib.wwObject.getDefault({
                            type: 'ww-icon',
                            data: {
                                icon: 'fas fa-circle',
                                style: {
                                    size: 12,
                                    fontSize: 12,
                                    color: '#343C38',
                                    borderWidth: 0
                                }
                            }
                        }),
                        title: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Early on we really wanted a seed stage focussed SaaS VC with entrepreneurial background. Alex and Thomas had built one of the largest independent SaaS companies in Europe and were our first choice.'
                                }
                            }
                        }),
                        subtitle: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Founder Scoutbee'
                                }
                            }
                        }),
                        isActive: false
                    },
                    {
                        image: wwLib.wwObject.getDefault({
                            type: 'ww-image',
                            data: {
                                url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_4.png?%27}twic=v1/quality=85/resize=1024',
                                zoom: 0.6,
                                position: { x: -14, y: -14.4 },
                                style: {
                                    borderRadius: 100,
                                    ratio: 1
                                    // minWidth: 540
                                }
                            }
                        }),
                        icon: wwLib.wwObject.getDefault({
                            type: 'ww-icon',
                            data: {
                                icon: 'fas fa-circle',
                                style: {
                                    size: 12,
                                    fontSize: 12,
                                    borderWidth: 0
                                }
                            }
                        }),
                        title: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Alex was my core advisor as an entrepreneur for structuring and negotiating my Series A'
                                }
                            }
                        }),
                        subtitle: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Founder Packlink'
                                }
                            }
                        }),
                        isActive: false
                    },
                    {
                        image: wwLib.wwObject.getDefault({
                            type: 'ww-image',
                            data: {
                                url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_6.png?%27}twic=v1/quality=85/resize=1024',
                                zoom: 0.63,
                                position: { x: 12.8, y: -11.5 },
                                style: {
                                    // minWidth: 510,
                                    ratio: 1,
                                    borderRadius: 100
                                }
                            }
                        }),
                        icon: wwLib.wwObject.getDefault({
                            type: 'ww-icon',
                            data: {
                                icon: 'fas fa-circle',
                                style: {
                                    size: 12,
                                    fontSize: 12,
                                    color: '#343C38',
                                    borderWidth: 0
                                }
                            }
                        }),
                        title: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: '42CAP is the ideal partner to build a global SaaS business'
                                }
                            }
                        }),
                        subtitle: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Founder Katana'
                                }
                            }
                        }),
                        isActive: false
                    },
                    {
                        image: wwLib.wwObject.getDefault({
                            type: 'ww-image',
                            data: {
                                url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_3.png?%27}twic=v1/quality=85/resize=1024',
                                zoom: 0.7,
                                position: { x: 0, y: 0 },
                                style: {
                                    borderRadius: 100,
                                    ratio: 1
                                    // minWidth: 540
                                }
                            }
                        }),
                        icon: wwLib.wwObject.getDefault({
                            type: 'ww-icon',
                            data: {
                                icon: 'fas fa-circle',
                                style: {
                                    size: 12,
                                    fontSize: 12,
                                    borderWidth: 0
                                }
                            }
                        }),
                        title: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: '42CAP had a conviction in the Gig Economy, which intrigued us.'
                                }
                            }
                        }),
                        subtitle: wwLib.wwObject.getDefault({
                            type: 'ww-text',
                            data: {
                                text: {
                                    en: 'Nicolas Rebout • CEO & Founder Shine'
                                }
                            }
                        }),
                        isActive: false
                    }
                ];
                needUpdate = true;
            }

            if (!this.section.data.contentList[0].subtitle) {
                this.section.data.contentList[0].subtitle = wwLib.wwObject.getDefault({
                    type: 'ww-text',
                    data: {
                        text: {
                            en: ''
                        }
                    }
                });
                needUpdate = true;
            }

            if (this.section.data.arrowIcon) {
                this.section.data.bottomList = [_.cloneDeep(this.section.data.arrowIcon)];
                delete this.section.data.arrowIcon;
                needUpdate = true;
            }

            if (!this.section.data.bottomList) {
                this.section.data.bottomList = [
                    wwLib.wwObject.getDefault({
                        type: 'ww-icon',
                        data: {
                            icon: 'fas fa-arrow-down',
                            style: {
                                size: 34,
                                borderWidth: 0
                            }
                        }
                    })
                ];
                needUpdate = true;
            }

            if (needUpdate) {
                this.sectionCtrl.update(this.section);
            }
        },
        startTimer() {
            if (wwLib.manager || window.__WW_IS_PRERENDER__) {
                return;
            }
            clearTimeout(this.timer);
            this.timer = setInterval(() => {
                this.showSlide((this.slideIndex += 1));
            }, 4000);
        },
        nextSlide() {
            this.showSlide(this.slideIndex + 1);
        },
        previousSlide() {
            this.showSlide(this.slideIndex - 1);
        },
        showSlide(index) {
            if (index > this.section.data.contentList.length - 1) {
                index = 0;
            }
            if (index < 0) {
                index = this.section.data.contentList.length - 1;
            }
            this.slideIndex = index;

            this.startTimer();
        },

        /* wwManager:start */
        add(list, options) {
            list.splice(options.index, 0, options.wwObject);

            this.sectionCtrl.update(this.section);
        },
        remove(list, options) {
            list.splice(options.index, 1);

            this.sectionCtrl.update(this.section);
        },
        getNewSlide() {
            const position = {
                left: { x: 7.7, y: 1.5 },
                right: { x: -12.2, y: 4.7 }
            };

            const zoom = {
                right: 0.556,
                left: 0.6
            };
            const objectData = {
                slide5: {
                    zoom: 0.6,
                    position: { x: 7.7, y: 1.5 }
                }
            };
            if ((this.section.data.contentList.length - 2) % 2 !== 0) {
                objectData.position = position.left;
                objectData.zoom = zoom.left;
            } else {
                objectData.position = position.right;
                objectData.zoom = zoom.right;
            }

            const defaultObj = {
                image: wwLib.wwObject.getDefault({
                    type: 'ww-image',
                    data: {
                        url: 'https://weweb.twic.pics/designs/436/sections/default.jpg?%27}twic=v1/quality=85/resize=1024',
                        zoom: objectData.zoom,
                        position: objectData.position,
                        style: {
                            ratio: 1,
                            borderRadius: 100
                        }
                    }
                }),
                icon: wwLib.wwObject.getDefault({
                    type: 'ww-icon',
                    data: {
                        icon: 'fas fa-circle',
                        style: {
                            size: 12,
                            fontSize: 12,
                            color: '#343C38',
                            borderWidth: 0
                        }
                    }
                }),
                title: wwLib.wwObject.getDefault({
                    type: 'ww-text',
                    data: {
                        text: {
                            en: 'Default text'
                        }
                    }
                }),
                subtitle: wwLib.wwObject.getDefault({
                    type: 'ww-text',
                    data: {
                        text: {
                            en: 'Default text'
                        }
                    }
                }),
                isActive: false
            };

            const card = JSON.parse(JSON.stringify(defaultObj));
            wwLib.wwUtils.changeUniqueIds(card);
            return card;
        },
        addSlide() {
            const newCard = this.getNewSlide();
            this.section.data.contentList.push(newCard);
            this.sectionCtrl.update(this.section);
        },
        removeSlide(index) {
            if (!this.section.data.contentList.length) {
                return;
            }
            if (!index) {
                index = this.slideIndex - 1;
            }

            this.section.data.contentList.splice(index, 1);

            this.sectionCtrl.update(this.section);
        }
        /* wwManager:end */
    }
};
</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style lang="scss" scoped>
.slider-section {
    position: relative;
    min-height: calc(100vh - 100px);

    &::v-deep {
        .background {
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            width: 100%;
        }

        .content-container {
            width: 100%;
            position: relative;
            /*max-width: 1200px;*/
            padding: 40px 10%;
            margin: auto;
            display: flex;
            justify-content: space-between;
            flex-direction: row;
            align-items: center;

            .left-container {
                width: 100%;
                max-width: 450px;
                position: relative;
                min-height: 200px;
                display: grid;

                .header {
                    grid-area: 1 / 1 / 2 / 2;
                    z-index: 0;
                    // visibility: hidden;
                    opacity: 0;
                    transition: opacity 0.2s ease;

                    &.show {
                        z-index: 1;
                        // visibility: visible;
                        opacity: 1;
                    }

                    .title {
                        font-style: normal;
                        font-weight: normal;
                        font-size: 24px;
                        line-height: 30px;
                        color: #343c38;
                    }

                    .subtitle {
                        margin-bottom: 30px;
                        margin-top: 10px;
                        font-style: normal;
                        font-weight: normal;
                        font-size: 18px;
                        line-height: 45px;
                        color: #343c38;
                    }
                }

                .icons-list {
                    display: flex;
                    flex-direction: row;
                    position: absolute;
                    bottom: 0;

                    .points {
                        display: flex;
                        flex-direction: row;
                        align-items: center;

                        .dot {
                            &.current {
                                .ww-icon {
                                    div {
                                        color: #67836f !important;
                                    }
                                }
                            }

                            .ww-icon {
                                div {
                                    margin: 0 5px;
                                    color: rgba(52, 60, 56, 0.32) !important;
                                }
                            }
                        }
                    }

                    .icon {
                        max-width: 40px;
                        cursor: pointer;
                    }

                    .ww-icon {
                        div {
                            margin: 0 8px;
                            cursor: pointer;
                        }
                    }
                }
            }

            .right-container {
                width: 100%;
                max-width: 700px;
                margin-left: 50px;
                height: 600px;
                position: relative;

                .slider-image {
                    position: absolute;
                    right: 0;
                    left: 0;
                    top: 50%;
                    transform: translateY(-50%);

                    display: none;

                    &.active {
                        display: block;
                    }

                    /*
                        NOW I SEND THE POSITION IN WWOBJECT PARAMETERS BUT YOU CAN USE THIS METHOD
                           &.image-0 img.image {
                                transform: translate(-62.2%, -45.3%) !important;
                            }

                            &.image-1 img.image {
                                transform: translate(-40.4%, -49%) !important;
                            }

                            &.image-2 img.image {
                              transform: translate(-45%, -51%) !important;

                            }
                         */

                    .format {
                        background: transparent;

                        img {
                            border-radius: 50%;
                        }
                    }
                }

                .main-image {
                    border-radius: 0;

                    .format {
                        img {
                            border-radius: 0;
                        }
                    }
                }
            }

            .edit-custom-block {
                display: flex;
                position: absolute;
                top: 15px;
                right: 20px;
                cursor: pointer;

                .icon-edit {
                    pointer-events: all;

                    .awesome-icon {
                        color: #ef811a;
                        font-size: 24px;
                        margin-left: 25px;
                    }
                }
            }
        }
    }
}

@media only screen and (max-width: 1100px) {
    .slider-section {
        &::v-deep {
            .content-container {
                .left-container {
                    min-height: 180px;
                    margin-top: 30px;

                    .header {
                        &.show {
                            &.main {
                                .title {
                                    font-size: 36px;
                                    line-height: 50px;
                                }
                            }
                        }

                        .title {
                            font-size: 20px;
                            line-height: 26px;
                        }

                        .subtitle {
                            font-size: 16px;
                            line-height: 36px;
                        }
                    }
                }

                .right-container {
                    height: 250px;
                }
            }
        }
    }
}

@media only screen and (max-width: 850px) {
    .slider-section {
        &::v-deep {
            .content-container {
                padding: 40px 20px;
                min-height: 200px;

                .right-container {
                    height: 280px;
                }
            }
        }
    }
}

@media only screen and (max-width: 768px) {
    .slider-section {
        &::v-deep {
            .content-container {
                padding: 30px 20px;

                flex-direction: column-reverse;

                .left-container {
                    position: unset;
                    margin-top: 50px;
                    min-height: 150px;
                    .header {
                        &.main {
                            margin-bottom: 32px;

                            .title {
                                margin: 20px 0;
                                font-size: 32px;
                                line-height: 40px;
                            }
                        }

                        .title {
                            margin: 20px 0;
                            font-size: 20px;
                            line-height: 30px;
                            color: #343c38;
                        }

                        .subtitle {
                            margin-bottom: 20px;
                            margin-top: 0;
                            font-size: 16px;
                            line-height: 25px;
                            color: #343c38;
                        }
                    }
                }

                .right-container {
                    margin: 0;
                    height: 500px;
                }
            }
        }
    }
}

@media only screen and (max-width: 640px) {
    .slider-section {
        &::v-deep {
            .content-container {
                .left-container {
                    margin-top: 20px;
                    min-height: 130px;
                }

                .right-container {
                    height: 450px;
                }
            }
        }
    }
}

@media only screen and (max-width: 500px) {
    .slider-section {
        &::v-deep {
            .content-container {
                .left-container {
                    margin-top: 20px;
                }

                .right-container {
                    height: 300px;
                }
            }
        }
    }
}

@media only screen and (max-width: 400px) {
    .slider-section {
        &::v-deep {
            .content-container {
                .left-container {
                    margin-top: 20px;
                }

                .right-container {
                    height: 250px;
                }
            }
        }
    }
}
</style>
