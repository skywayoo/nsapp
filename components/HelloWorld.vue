<template>
    <Page>
        <ActionBar title="CHPT - SN辨識" />
        <ScrollView>
            <StackLayout class="home-panel">
                <!--Add your page content here-->
                <StackLayout orientation="vertical" iosOverflowSafeArea="false">
					<Image v-if="pictureFromCamera" class="mainImage" :src="pictureFromCamera" @tap="takePicture"/>
					<Image v-else class="mainImage" :src="path_main" @tap="takePicture" />
                </StackLayout>
                <StackLayout class="dv-line" height="3" backgroundColor="#6a737b"/>
                <StackLayout>
                    <Label text="Serial Number (SN):" class="input-title" style="font-weight: bold;"/>
                    <Label :text="sn_text" class="input-desc" textWrap="true" />
                </StackLayout>
                <StackLayout class="dv-line" height="2" backgroundColor="#6a737b"/>
                <StackLayout>
                    <Label text="Product Number (P/N):" class="input-title" style="font-weight: bold;" />
                    <Label :text="pn_text" class="input-desc" textWrap="true" />
                </StackLayout>                
                <StackLayout class="dv-line" height="3" backgroundColor="#6a737b"/>
                <Button class="mainbutton" text="Upload & Key-In" @tap="uploadSn" />
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
    import * as camera from "nativescript-camera";
    import * as bghttp from "nativescript-background-http";
    import {Image} from "tns-core-modules/ui/image";
    import { request } from "tns-core-modules/http";
    import * as dialogs from "tns-core-modules/ui/dialogs";
    const imageSourceModule = require("tns-core-modules/image-source");
    const fileSystemModule = require("tns-core-modules/file-system");
    export default {
        methods: {
            takePicture() {
                camera.requestPermissions();
                camera
                    .takePicture({
                        width: 700,
                        height: 700,
                        saveToGallery: false,
                        keepAspectRatio: true
                    })
                    .then(picture => {
                        this.pictureFromCamera = picture;
                        this.sn_text = "Uploading...";
                        this.pn_text = "Uploading...";

                        const path = this.pictureFromCamera._android
                        //var main_path_1 = path;
                        //console.log("save path = ",path);
                        //main_path_1 = main_path_1.split("/")
                        //this.path_main = main_path_1.pop();
                        //console.log("this.path_main = ",main_path_1);
                        //this.path_main =  main_path_1.join("/") + "/app/images/mainimage.jpg" 
                        //console.log("this.path_main(new) = ",this.path_main);
                        var name = path.substr(path.lastIndexOf("/") + 1);
                        console.log("name",name);
                        var session = bghttp.session("image-upload");
                        var request = {
                            url: "http://TEST/upload/test/image",
                            method: "POST",
                            headers: {
                                "Content-Type": "application/octet-stream",
                                "File-Name": name,
                            },
                            description: "{ 'uploading': " + name + " }",
                            androidNotificationTitle: "File Uploading to Server..."
                        };
                        try {
                            var task = session.uploadFile(path, request);
                            try {
                                console.log("task info:", task);
                                task.on("progress", args => {
                                    console.log(args)
                                    let progess_bar = (args.currentBytes/args.totalBytes)*100
                                    this.sn_text = "Uploading... -> " + progess_bar + "%";
                                    this.pn_text = "Uploading... -> " + progess_bar + "%";
                                });
                                task.on("error", error => {
                                    console.log("error");
                                    console.log(error.response);
                                    console.log(error.responseCode);
                                    this.sn_text = "Upload Error";
                                    this.pn_text = "Upload Error";
                                });
                                task.on("complete", resp => {
                                    //console.log(resp)
                                    console.log("complete");
                                });
                                task.on("responded", resp => {
                                    let response_val = JSON.parse(resp.data)
                                    console.log("response = ", response_val)
                                    this.sn_text = response_val['sn'];
                                    this.pn_text = response_val['pn'];
                                });
                            } catch (err) {
                                console.log("REQUEST", err);
                                this.sn_text = "REQUEST - ERROR CODE 201";
                                this.pn_text = "REQUEST - ERROR CODE 201";
                            }
                        } catch (err) {
                            this.sn_text = "REQUEST - ERROR CODE 202";
                            this.pn_text = "REQUEST - ERROR CODE 202";
                            console.log("REQUEST", err);
                        }
                    })
            },
            uploadSn(){
                if (/^\d+/.test(this.sn_text)){
                    request({
                        url: "http://TEST/uploadPC/test/"+this.sn_text,
                        method: "POST"
                    }).then((response) => {
                        console.log(response)
                        dialogs.alert({
                        title: "提示訊息",
                        message: "Key-In 成功",
                        okButtonText: "確認"
                        }).then(() => {
                            console.log("Dialog closed!");
                        });
                    }, (e) => {
                        console.log(e)
                    });
                }
                else{
                    dialogs.alert({
                        title: "提示訊息",
                        message: "請辨識出SN/PN數值後，再執行上傳",
                        okButtonText: "確認"
                    }).then(() => {
                        console.log("Dialog closed!");
                    });
                }
            }

        },
        data() {
            return {
                sn_text: "\n",
                pn_text: "\n",
                pictureFromCamera: null,
                predictedName: null,
                path_main:"/storage/emulated/0/Android/data/org.nativescript.playvue/mainimage.jpg",
                images: []
            };
        }
    };
</script>

<style scoped>
    .home-panel {
        vertical-align: center;
        font-size: 20;
        margin: 15;
    }

    .description-label {
        margin-bottom: 15;
    }
    .mainImage {
        margin: 10px;
        width : 100%;
        height : 300;
    }
    .dv-line {
        margin : 30px;
        width : 100%;

    }
    .mainbutton {
        background-color: #1c79c0;
        margin-top: 20;
        padding: 0;
        border-radius: 5;
        height: 100;
        border-color: transparent;
        border-width: 1;
        font-size: 30px;
        color : white;
    }
</style>