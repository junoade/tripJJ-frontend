<script setup>

import { ref } from 'vue';
import StoryFileUploadView from "@/components/snapshot/StoryFileUploadView.vue";
import StoryFileUploadCheck from "@/components/snapshot/StoryFileUploadCheck.vue";
import StoryFileUpload from "@/components/snapshot/StoryFileUpload.vue";

const isFileUpload = ref(false); // 업로드 이미지를 올렸는가?
const isFileChecked = ref(false); // 업로드 이미지를 확인했는가?
const images = ref([]); // blob:URL; 클라이언트(로컬)에서만 확인하기 위해
const fileList = ref([]); // 서버로 전달될 실제 파일


/**
 * 자식 컴포넌트에서 닫기 버튼을 눌렀다면 모든 상태를 초기화한다.
 */
const clearStatus = () => {
    console.log("초기화");
    isFileUpload.value = false;
    isFileChecked.value = false;
    images.value = [];
}

/**
 * 자식 컴포넌트(사용자가 업로드한 파일 관리)로부터 이벤트를 수신하고 부모 컴포넌트의 반응형 변수들을 갱신한다.
 * @param {*} uploadedImages (Object)
 */
const afterUploadImages = (uploadedImages, uploadedFiles) => {

    if(uploadedImages.length === 0) {
        alert("여행 이미지를 등록해주세요!");
        return;
    }

    isFileUpload.value = true;
    images.value = uploadedImages;
    fileList.value = uploadedFiles;
    console.log("자식 컴포넌트로부터 업로드된 이미지 파일을 전달받았다!", images.value);
}

/**
 * 자식 컴포넌트(사용자가 확인한 업로드한 파일)로부터 이벤트 수신하고 부모 컴포넌트의 반응형 변수 갱신
 */
const afterCheckUploadImages = (status) => {
    if (status) {
        console.log("업로드될 이미지들을 확인 완료!");
        console.log("업로드될 이미지 목록 : ", images.value);
        isFileChecked.value = true;

    } else {
        console.log("업로드될 이미지 목록 수정할래");
        isFileUpload.value = false;
    }
}

const goBackToCheckUploadImages = () => {
    isFileChecked.value = false;
    console.log("업로드될 이미지 목록 확인하러 갈래");
}

</script>

<template>
    <div class="modal fade" id="exampleModal" tabindex="-1" :aria-labelledby="exampleModalLabel" aria-hidden="true">
        <template v-if="!isFileUpload">
            <StoryFileUploadView :images="images" @afterUploadImages="afterUploadImages" @clearStatus="clearStatus" />
        </template>
        <template v-else-if="isFileUpload && !isFileChecked">
            <StoryFileUploadCheck :images="images" @afterCheckUploadImages="afterCheckUploadImages"
                @clearStatus="clearStatus" />
        </template>
        <template v-else-if="isFileUpload && isFileChecked">
            <StoryFileUpload :images="images" :fileList="fileList"
                @checkUploadImages="goBackToCheckUploadImages" 
                @clearStatus="clearStatus" />
        </template>
    </div>
</template>

<style scoped></style>