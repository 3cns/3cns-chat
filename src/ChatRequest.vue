<template>
    <div class="chat-request">
        <div v-if="!chat">
            <img class="common-icon" id="show" v-if="showWidget" @click="openSideBar" :src="widgetButton.src"
                 :title="widgetButton.title" :alt="widgetButton.title">
        </div>
        <div class="side-bar slideInRight animated" v-if="showSideBar && !chat">
      <span class="close-form hide1 cross" @click="closeSideBar">
        <img :src="apiHost +'widgets/script/chat-close.png'" alt="img">
      </span>
            <h3 class="side-logo"><img :src="widgetLogo.src" alt=""></h3>
            <div v-if="!formSubmit">
                <h2 v-if="isAvailable || !chatScheduleClicked">Get In Touch With Us</h2>
                <div class="formcontainer">
                    <div class="col-lg-12">
                        <h3 v-if="chatScheduleClicked">
                            <p v-if="!isAvailable">We are unable to chat with you now.</p>
                            <p>Schedule a time to chat!</p>
                        </h3>
                    </div>
                    <div class="side-arrow hide1" @click="closeSideBar">
                        <icon name="chevron-right"></icon>
                    </div>
                    <form @submit.prevent="validateBeforeSubmit">
                        <div class="row" v-if="chatScheduleClicked">
                            <div class="col-sm-5">
                                <div class="form-group" :class="{ 'has-error': errorSchedule }">
                                    <select class="form-control" v-model="selectedDay">
                                        <option :value="null">Select Date</option>
                                        <option :value="day" v-for="(day) in availableDays">{{ day }}</option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-xs-1"><span class="at">at</span></div>
                            <div class="col-sm-5">
                                <div class="form-group" :class="{ 'has-error': errorSchedule }">
                                    <select class="form-control" v-model="selectedTime" :disabled="selectedDay==null">
                                        <option :value="null">Select Time</option>
                                        <option :value="time" v-for="(time, key) in availableTimes" :key="time.start">{{
                                            time }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-sm-12" v-if="errorSchedule">
                                <div class="has-error">
                  <span class="help-block">
                    Please enter a valid schedule
                  </span>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-12">
                                <span class="timezone"
                                      v-if="chatScheduleClicked">*Time shown in {{ this.timezone }}</span>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-10">
                                <div class="form-group" :class="{ 'has-error': $v.name.$error }">
                                    <input class="form-control" type="text" v-model="name" placeholder="Enter Your Name"
                                           @blur="$v.name.$touch()">
                                    <span v-if="($v.name.$error  )  " class="help-block">
                    Your name is required!
                  </span>
                                </div>
                            </div>
                            <div class="col-md-10">
                                <div class="form-group" :class="{ 'has-error': $v.email.$error  }">
                                    <input v-model="email" class="form-control" type="text"
                                           placeholder="Enter Your Email ID" @blur="$v.email.$touch()">
                                    <span v-if="($v.email.$error)" class="help-block">
                    Your email is required!
                   </span>
                                </div>
                            </div>
                            <div class="col-md-10">
                                <div class="form-group"
                                     :class="{ 'has-error': $v.phoneField.$error  || errorPhoneField}">
                                    <input class="form-control" type="text" v-mask="'+1(###)-###-####'"
                                           v-model="phoneField" placeholder="Enter Your Phone Number"
                                           @blur="$v.phoneField.$touch()">
                                    <span v-if="($v.phoneField.$error )" class="help-block">
                    Your phone number is required!
                  </span>
                                    <span v-if="errorPhoneField" class="help-block">
                    Please enter a valid phone number
                  </span>
                                </div>
                            </div>
                        </div>
                        <br>
                        <div class="row" v-if="btnProp.showChatNow">
                            <div class="col-md-10">
                                <div class="form-group">
                                    <button class="btn btn-primary" type="submit" :disabled="$v.$invalid">
                                        Submit
                                    </button>
                                </div>
                            </div>
                        </div>
                        <div class="row" v-if="btnProp.showChatLater">
                            <div class="col-md-10">
                                <div class="form-group">
                                    <button class="btn btn-primary" type="submit" :disabled="$v.$invalid">Submit
                                    </button>
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
                <span v-if="btnProp.showChatSchedule"><a @click="chatSchedule">{{ chatScheduleTitle }}</a></span>
            </div>
            <div v-if="formSubmit" id="department">
                <div class="container" v-if="!checkMobile">
                    <div class="side-arrow hide1" @click="closeSideBar">
                        <icon name="chevron-right"></icon>
                    </div>
                    <div class="col-md-8">
                        <div class="col-md-5" v-if="!departmentFormSubmit">
                            <div v-if="widgetDepartments.length > 1">
                                <div class="form-group">
                                    <label class="control-label">
                                        Choose a department
                                    </label>
                                    <div>
                                        <div v-for="department in widgetDepartments" class="list-group">
                                            <a class="list-group-item" @click="departmentSubmit(department.id)"> {{
                                                department.department_name }} </a>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div v-else>
                                <div class="loader"></div>
                            </div>
                        </div>
                        <div class="col-md-5" v-if="departmentFormSubmit">
                            <div class="panel-body">
                                <div v-if="chatScheduleClicked">
                                    <p>
                                        Thank you for showing interest in our platform .One of our agents will chat with
                                        you at the specified time.
                                    </p>
                                </div>
                                <div v-if="!chatScheduleClicked">
                                    <p>
                                        Thank you for showing interest in our platform . To start chatting click the
                                        button given below.
                                    </p>
                                    <button type="button" class="btn btn-primary" @click="startChat"> Start Chat
                                    </button>
                                </div>
                                <div class="col-md-12 cust-pad">
                                    <!-- <span style='display: block;text-decoration: underline; cursor: pointer;' id='again'>Chat again</span> -->
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="container" v-if="checkMobile">

                </div>
            </div>
        </div>

        <chat v-if="chat"></chat>
    </div>
</template>

<script>

    import Vue from 'vue';
    import {required, email, minLength, requiredIf, numeric} from 'vuelidate/lib/validators';
    import {environment} from './environment/environment';
    import "bootstrap/dist/css/bootstrap.css";

    export default {
        name: 'chat-request',
        data() {
            return {
                messages: [
                    {
                        message: 'Hi',
                        author: 'Danny',
                    },
                    {
                        message: 'Hey',
                        author: 'John',
                    },
                ],
                chat: false,
                msg: 'Welcome to Your Vue.js App',
                showWidget: false,
                isAvailable: false,
                errorSchedule: false,
                errorPhoneField: false,
                showSideBar: false,
                timezone: null,
                phoneField: '',
                name: '',
                email: '',
                selectedDay: null,
                selectedTime: null,
                widgetId: null,
                widgetHost: null,
                widgetTimezone: null,
                widgetDepartments: {},
                widget: {},
                availableDays: {},
                time: {
                    nowInLocal: '',
                    utc: '',
                    nowInUTC: '',
                    cur_date_UTC: '',
                    cur_time_UTC: '',
                    first_val: '',
                    schedules: '',
                    secheduleTimes: '',
                    start_val: '',
                    end_val: ''
                },
                widgetButton: {
                    title: '',
                    src: ''
                },
                widgetLogo: {
                    title: '',
                    src: ''
                },
                chatScheduleClicked: false,
                chatScheduleTitle: 'Choose a time to call',
                btnProp: {
                    showChatSchedule: false,
                    showChatNow: true,
                    showChatLater: false,
                },
                formSubmit: false,
                departmentFormSubmit: false,
                dataToSend: {},
                checkEmail: false,
                checkMobile: '',
                apiUrl: '',
                apiHost: '',
                currentTime: ''
            }
        },

        // Validation
        validations: {
            email: {
                required,
                email
            },
            name: {
                required
            },
            phoneField: {
                required
            }
        },
        created() {

            if (Vue.ls.get('client')) {
                Vue.ls.remove('client');
            }


            this.apiUrl = environment.API_BASE_URL;
            this.apiHost = environment.API_HOST;
            console.log(this.apiUrl);
            this.widgetId = document.getElementById('tib-widget').getAttribute('data-uuid');
            //this.widgetHost = document.getElementById('tib_widget').src.split(':')[0] + ':\/\/' + document.getElementById('tib_widget').src.split('/')[2];
            this.$http.post(this.apiUrl + 'widget-data', {widgetUuid: this.widgetId})
                .then(
                    (response) => {
                        console.log(response);
                        if (response.status) {
                            if (response.body.status) {
                                this.widget = response.body.widget;
                                this.widgetTimezone = response.body.timezone;
                                this.availableDays = response.body.dates;
                                let requiredUrl = response.url;
                                // Add Checking 'requiredUrl' will not match
                                const currentUrl = location.protocol + '\/\/' + location.host;
                                //requiredUrl = 'http://localhost:8080';
                                // if(requiredUrl === currentUrl){
                                //   this.showWidget = true;
                                //   this.checkDevice();
                                //   this.showButton();

                                // }
                                this.showWidget = true;
                                this.checkDevice();
                                this.showButton();
                            }
                        }
                    },
                    (error) => {
                        this.showWidget = false;
                        console.error(error);
                    }
                );


        },
        watch: {
            chatScheduleClicked(value) {
                if (value) {
                    this.chatScheduleTitle = 'We can chat with you now';
                } else {
                    this.chatScheduleTitle = 'Choose a time to chat';
                }
                this.configureView();
            }
        },
        computed: {
            availableTimes() {
                this.selectedTime = null;
                if (this.selectedDay != null) {
                    const optionTime = [];
                    let startTime = this.time.start_val;
                    const endTime = this.time.end_val;
                    let i, diff;
                    for (i = startTime; i <= endTime; i++) {
                        if (i < 12) {
                            optionTime.push(i + 'am');
                        } else {
                            diff = endTime - 12;
                            optionTime.push(diff + ' pm');
                        }
                    }
                    return optionTime;
                }
                return null;
            }
        },
        methods: {
            validateBeforeSubmit() {
                if (this.chatScheduleClicked) {
                    if (this.validateSchedule() && this.validatePhoneNumber()) {
                        this.chatLater();
                    }
                } else {
                    if (this.validatePhoneNumber()) {
                        this.chatNow();
                    }
                }
            },
            departmentSubmit(id) {
                this.departmentFormSubmit = true;
                this.dataToSend.departmentId = id;

                console.log(this.dataToSend);

                /** api call to send the chat schedule  */

            },
            openSideBar() {
                this.showSideBar = true;
            },
            closeSideBar() {
                this.showSideBar = false;
            },
            formatDate(date) {
                const d = new Date(date);
                let month = '' + (d.getMonth() + 1),
                    day = '' + d.getDate(),
                    year = d.getFullYear();

                if (month.length < 2) {
                    month = '0' + month;
                }
                if (day.length < 2) {
                    day = '0' + day;
                }
                return [year, month, day].join('-');
            },
            configureView() {
                this.widgetButton.title = 'Chat with us';
                this.widgetButton.src = this.apiHost + 'widgets/chat-btn.png';
                this.widgetLogo.src = this.widget.image;
                this.btnProp.showChatSchedule = this.isAvailable;
                if (this.chatScheduleClicked) {
                    this.btnProp.showChatNow = false;
                    this.btnProp.showChatLater = true;
                } else {
                    this.btnProp.showChatNow = true;
                    this.btnProp.showChatLater = false;
                }

            },
            chatSchedule() {
                this.chatScheduleClicked = !this.chatScheduleClicked;
            },
            validateSchedule() {
                let date = this.selectedDay;
                let time = this.selectedTime;
                if (date == undefined || time == undefined || date == null || time == null || date == '' || time == '') {
                    this.errorSchedule = true;
                    return false;
                }
                this.errorSchedule = false;
                return true;
            },
            validatePhoneNumber() {
                let phoneNumberPattern = this.phoneField.replace(/[\s()+-]+/g, '');
                if (phoneNumberPattern != '') {
                    if (phoneNumberPattern.length == 11 && phoneNumberPattern.charAt(0) !== 1) {
                        this.errorPhoneField = false;
                        return true;
                    } else {
                        this.errorPhoneField = true;
                        return false;
                    }
                } else {
                    this.errorPhoneField = true;
                    return false;
                }
            },
            sendData(client_name, client_email, client_phone, date, time) {
                let start, end;
                if (time != undefined) {

                    if (time.split(' ')[1] == 'am') {
                        start = time.split(' ')[0].indexOf(':') > -1 ? time.split(' ')[0] : time.split(' ')[0] + ':00';

                    } else {
                        start = time.split(' ')[0].indexOf(':') > -1 ? parseInt(time.split(' ')[0].split(':')[0]) >= 12 ? time.split(' ')[0] : (parseInt(time.split(' ')[0].split(':')[0]) + 12) + ':' + time.split(' ')[0].split(':')[1] : parseInt(time.split(' ')[0].split(':')[0]) >= 12 ? time.split(' ')[0].split(':')[0] + ':00' : (parseInt(time.split(' ')[0].split(':')[0]) + 12) + ':00';

                    }
                }

                this.dataToSend = {

                    name: client_name,
                    email: client_email,
                    fromNumber: client_phone,
                    widgetUuid: this.widgetId,
                    scheduleDate: date != undefined ? date : 0,
                    scheduleTime: start != undefined ? start.split(':')[0] + ':00:00' : 0,

                };

                this.formSubmit = true;

                /** api call to get the departments for the widget */
                this.$http.post(this.apiUrl + 'widget-departments', {widget_data: this.dataToSend})
                    .then(
                        (response) => {
                            if (response.status) {
                                if (response.body.status) {
                                    const departmentLength = response.body.departments.length;
                                    if (departmentLength > 1) {
                                        this.widgetDepartments = response.body.departments
                                    } else if (departmentLength == 1) {
                                        this.departmentSubmit(response.body.departments[0].id)
                                    } else {
                                        console.log('No department found');
                                    }
                                }
                            }
                        },
                        (error) => {
                            console.error(error);
                        }
                    );

            },
            showButton() {
                const min_width = 600;
                const responsive_width = 991;
                const device_width = window.screen.width;


                this.timezone = (this.widgetTimezone.timezone_name).replace(/ *\([^)]*\) */g, "");
                this.currentTime = this.widgetTimezone.current_time;


                this.time.nowInLocal = new Date();
                this.time.utc = new Date(this.time.nowInLocal.getTime() + this.time.nowInLocal.getTimezoneOffset() * 60000);
                this.time.nowInUTC = new Date(this.time.utc.getTime() + (parseInt(this.widgetTimezone.time_difference.split(":")[0]) * 60 * 60000));
                this.time.cur_date_UTC = this.formatDate(this.time.nowInUTC);
                this.time.cur_time_UTC = this.time.nowInUTC.getHours();
                this.time.cur_day_UTC = this.time.nowInUTC.getDay();
                this.time.schedules = this.widgetTimezone.days;
                if (this.time.schedules.hasOwnProperty(this.time.cur_day_UTC)) {
                    this.secheduleTimes = this.widgetTimezone.dayTime;
                    this.time.start_val = parseInt(this.secheduleTimes[this.time.cur_day_UTC].start_time.toString().split(":")[0]);
                    console.log(this.time.cur_date_UTC);
                    this.time.end_val = parseInt(this.secheduleTimes[this.time.cur_day_UTC].end_time.toString().split(":")[0]);
                    if (this.currentTime >= this.time.start_val && this.currentTime < this.time.end_val) {
                        console.log('true: available');
                        this.btnProp.showChatSchedule = true;
                        this.chatScheduleClicked = false;
                        this.isAvailable = true;
                    } else {
                        console.log("true: not available");
                        this.btnProp.showChatSchedule = false;
                        this.chatScheduleClicked = true;
                        this.isAvailable = false;
                    }
                } else {
                    console.log('false: not available');
                    this.btnProp.showChatSchedule = false;
                    this.chatScheduleClicked = true;
                    this.isAvailable = false;
                }
                this.configureView();

            },
            chatNow() {
                this.sendData(this.name, this.email, this.phoneField);
            },
            chatLater() {
                this.sendData(this.name, this.email, this.phoneField, this.selectedDay, this.selectedTime);
            },
            /** to check if mobile or desktop */
            checkDevice() {
                const min_width = 600;
                const device_width = window.screen.width;
                this.checkMobile = false;

                // Desktop
                if (device_width > min_width) {
                    this.checkMobile = false;
                }
                // Mobile
                if (device_width <= min_width) {
                    this.checkMobile = true;
                }
            },
            startChat() {
                console.log("Start chat");
                Vue.ls.set('client', this.dataToSend);
                console.log(Vue.ls.get('client'));
                this.chat = true;

            },
            sendChatMessage(message) {
                this.dataToSend.message = message;
                console.log(this.dataToSend);
            }
        }
    }
</script>

<style>


    a {
        cursor: pointer;
    }

    .at {
        display: block;
        float: left;
        width: 10%;
        height: 45px;
        margin: 0px;
        line-height: 40px;
        color: #999;
    }

    .common-icon {
        position: fixed;
        right: 50px;
        bottom: 100px;
        z-index: 999;
        width: auto;
        height: auto;
        cursor: pointer;
        display: block;
    }

    .side-bar {
        position: fixed;
        top: 100px;
        bottom: 0px;
        z-index: 9999;
        background: rgb(240, 240, 240) none repeat scroll 0% 0%;
        height: auto;
        width: 393px;
        padding: 10px 20px 20px;
        box-shadow: rgb(178, 178, 178) 1px 0px 20px 1px;
        color: rgb(51, 51, 51);
        text-align: center;
        overflow-y: auto;
        right: 0px;
        font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
        overflow-x: hidden;
    }

    .side-bar h2 {
        text-align: center;
        text-transform: capitalize;
        margin: 13px 0px 15px;
        color: rgb(94, 94, 94);
        font-weight: bold;
        font-size: 20px;
    }

    .side-bar h3 {
        padding-bottom: 8px;
        font-size: 20px;
        margin-bottom: 10px;
        color: #5E5E5E;
        text-align: left;
        margin-top: 0;
    }

    .side-bar select {
        appearance: none !important;
        -webkit-appearance: none !important;
        -moz-appearance: none !important;
    }

    .side-bar .form-control {
        height: 35px;
        text-align: center;
    }

    .side-bar .cust-pad {
        padding: 0 5px;
    }

    .close-form {
        position: absolute;
        right: 10px;
        top: 10px;
        color: #B3B3B3;
        font-size: 14px;
        text-decoration: none;
        height: 20px;
        width: 20px;
        cursor: pointer;
    }

    .close-form img {
        max-width: 100%;
    }

    .hide1 {
        position: absolute;
        top: 7px;
        right: 10px;
        z-index: 9999;

    }

    .cross {
        width: 30px;
        height: 30px;
    / / border: 1 px solid rgb(94, 94, 94);
        border-radius: 50%;
        text-align: center;
        font-size: 19px;
        color: rgb(94, 94, 94);
    }

    h3.side-logo {
        width: 200px;
        margin: 15px auto 40px;
    }

    .formcontainer {
        background: rgb(249, 249, 249) none repeat scroll 0% 0%;
        padding-left: 40px;
        padding-top: 20px;
        margin: 8px;
        border-radius: 10px;
    }

    .error-msg-scheduled-call {
        text-align: center;
        color: red;
        display: block;
        margin-bottom: 17px;
    }

    .side-arrow {
        position: absolute;
        top: 60%;
        left: 10px;
        display: block;
        margin: 15px auto 40px;
        transform: translate(-50%, -50%);
        cursor: pointer;
        color: rgba(52, 100, 246, 1);
    }

    .powredby {
        text-align: center;
        margin-bottom: 20px;
        color: #5E5E5E;
        padding-top: 16px;
        bottom: 0;
        left: 0;
        right: 0;
        position: absolute;
    }

    .powredby img {
        width: 75px;
    }

    h3.side-logo img {
        max-width: 100%;
    }

    h3.side-logo {
        margin: 15px auto 5px !important;
    }

    .timezone {
        display: inline-block;
        margin: 0px 0px 10px;
        color: rgb(255, 87, 34);
        font-size: 12px;
    }
    .loader {
        border: 16px solid #f3f3f3;
        border-radius: 50%;
        border-top: 16px solid blue;
        border-right: 16px solid green;
        border-bottom: 16px solid red;
        border-left: 16px solid pink;
        width: 120px;
        height: 120px;
        -webkit-animation: spin 2s linear infinite;
        animation: spin 2s linear infinite;
    }

    @-webkit-keyframes spin {
        0% { -webkit-transform: rotate(0deg); }
        100% { -webkit-transform: rotate(360deg); }
    }

    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }

</style>

