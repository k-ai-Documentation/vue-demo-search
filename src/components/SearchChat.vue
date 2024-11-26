<template lang="pug">
.search-chat
    .message-history
        .message(v-for="message in messageHistory" :key="message.message" :style="{'justify-content': message.from === 'user' ? 'flex-end' : 'flex-start'}")
            .assistance-message(v-if="message.from === 'assistance'")
                button.btn-icon.text-bold-14 BOT
                .message-text.assistance-color 
                    p.text-regular-14 {{message.message}}
            .user-message(v-else)
                .message-text.user-color 
                    p.text-regular-14 {{message.message}}
                button.btn-icon.text-bold-14 USER
    .input-container
        input.simple-input-h30(type="text" placeholder="Type your message here..." @keyup.enter="sendMessage" v-model="userMessage") 
        button.btn-outline-rounded-30(@click="sendMessage") Send

</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { KaiStudio } from 'kaistudio-sdk-js';

// if you are using Saas
const kaiSearch = new KaiStudio({ organizationId: process.env.VUE_APP_ORGANIZATION_ID, instanceId: process.env.VUE_APP_INSTANCE_ID, apiKey: process.env.VUE_APP_API_KEY });
const needFollowingQuestions: boolean = process.env.VUE_APP_NEED_FOLLOWING_QUESTIONS === 'true';
const multiDocuments: boolean = process.env.VUE_APP_MULTI_DOCUMENTS === 'true';


const messageHistory = ref<{ from: string; message: string }[]>([]);
const userMessage = ref('');
const foundQuestion = ref(false);
const correctQuestion = ref('')

const sendMessage = async() => {
    messageHistory.value.push({ from: 'user', message: userMessage.value });
    userMessage.value = '';
    await identifySpecificDocument();
    if (foundQuestion.value) {
        const result = await search();
        messageHistory.value.pop();
        if(result.gotAnswer){
            messageHistory.value.push({ from: 'assistance', message: "Answer:" });
            messageHistory.value.push({ from: 'assistance', message: result.answer });

            if(result.documents.length > 0 ) {
                let documents = ''
                for( let i = 0; i < result.documents.length; i++) {
                    documents += "name:" + result.documents[i].name + '\n' + "url:" + result.documents[i].url + '\n\n';
                }
                messageHistory.value.push({ from: 'assistance', message: "Source:" });
                messageHistory.value.push({ from: 'assistance', message: documents });
            }
            
            if(needFollowingQuestions) {
                messageHistory.value.push({ from: 'assistance', message: "following questions:" });
                let questions = ''
                for( let i = 0; i < result.followingQuestions.length; i++) {
                    questions += ' - ' + result.followingQuestions[i] + '\n';
                }
                messageHistory.value.push({ from: 'assistance', message: questions });
            }
        }else {
            messageHistory.value.push({ from: 'assistance', message: "Sorry, I couldn't find an answer to your question." });
        }
        
        foundQuestion.value = false;
        correctQuestion.value = '';
    }
}

async function identifySpecificDocument() {
    const result = await kaiSearch.search().identifySpecificDocument(messageHistory.value);
    if (!result) {
        return;
    }
    if (result.isFinal == true) {
        foundQuestion.value = true;
        correctQuestion.value = result.question;
        messageHistory.value.push({ from: 'assistance', message: "Correct question found: " + result.question }); 
        messageHistory.value.push({ from: 'assistance', message: "Searching for answer..." });
    }else {
        messageHistory.value.push({ from: 'assistance', message: result.question });
    }
}

async function search() {
    const result = await kaiSearch.search().query(correctQuestion.value, 'userid', '',multiDocuments, needFollowingQuestions );
    return result;
}



onMounted(() => {
    messageHistory.value.push({ from: 'assistance', message: 'Hello, how can I help you today?' });
});
</script>

<style lang="scss" scoped>
.search-chat {
    width: 799px;
    height: 100%;
    background-color: var(--dark-grey-color);
    border: 1px solid var(--color-border);
    border-radius: 12px;
    padding: 11px 11px 30px 11px;
    display: flex;
    flex-direction: column;
    overflow: scroll;

    .message-history {
        width: 100%;
        height: 100%;
        border-radius: 10px;
        background-color: white;
        padding: 20px;
        overflow: scroll;

        .message {
            display: flex;
            flex-direction: row;
            width: 100%;     
            
            button {
                height: 50px;
                width: 50px;
                justify-content: center;
            }

            .assistance-message {
                display: flex;
                flex-direction: row;
                width: fit-content;
                max-width: 80%;
                align-items: center;
                .assistance-color {
                    background-color: var(--grey-5-color);
                }
            }

            .user-message {
                display: flex;
                flex-direction: row;
                width: fit-content;
                max-width: 80%;
                align-items: center;
                .user-color {
                    background-color: var(--grey-color);
                }
            }

            .message-text {
                width: 100%;
                margin: 10px 20px;
                color: white;
                padding: 10px 10px 10px 10px;
                border-radius: 10px;
                word-break: break-word;
                white-space: pre-wrap;
            }
        }
    }
    .input-container {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
        margin-top: 20px;

        input {
            background-color: white;
            color: black;
        }
    }
}
</style>
