<template>
  <v-container max-width="1200px">

    <v-spacer :style="{ height: '20px' }"></v-spacer>

    <h2 style="text-align:center; color:#094F08;">프로젝트 모집글 수정</h2>

    <v-spacer :style="{ height: '20px' }"></v-spacer>

    <v-row class="mt-10 mb-10">
      <v-text-field label="제목"
      type="title"
      v-model="title"
      placeholder="프로젝트의 제목을 작성해주세요."
      variant="plain"
      class="font-weight-bold"></v-text-field>
    </v-row>

    <v-row class="mt-10 mb-10">
      <v-file-input label="프로젝트 이미지" accept="image/" @change="fileUpdate" variant="underlined" rounded="xs">
      </v-file-input>
    </v-row>
    <v-row v-if="this.projectImageUrl" class="justify-center">
      <img :src="this.projectImageUrl" style="height:auto; width:500px;">
    </v-row>

    <v-row class="mt-10 mb-10">
      <v-text-field label="한줄 설명" type="text" id="description" v-model="description" variant="underlined"
        rounded="xs"></v-text-field>
    </v-row>

    <v-spacer :style="{ height: '20px' }"></v-spacer>

    <!-- 모집 기한 -->
    <v-row class="mt-10 mb-10">
      <h3 class="mr-5"> 모집 기한 </h3>

      <input type="datetime-local" v-model="deadline">

      <!-- <input type="date" id="deadline" v-model="deadline" /> -->

    </v-row>
    <!-- 마감 여부 꾸미기 -->
    <!-- <v-row  class="mt-10 mb-10 align-center ">
      <h3 class="mr-5"> 모집 마감 </h3>

      <v-switch class="align-center" v-model="this.isClosed"  false-value="N" true-value="Y"
        hide-details></v-switch>
    </v-row> -->
    <!-- <v-row>
      <input type="date" id="deadline" v-model="deadline" />
    </v-row> -->

    <!-- <v-spacer :style="{ height: '20px' }"></v-spacer> -->

    <!-- 모집 정보 추가 -->
    <v-row class="mt-10 align-center justify-start">
      <!-- 추가 버튼 -->
      <h3 style="margin-right: 20px;"> 모집 정보 </h3>
      <ButtonComponent content="추가" class="mr-3" @click="recruitInfoAdd()" />
    </v-row>


    <!-- 모집 정보 리스트 표시 -->
    <v-row class="mt-10 mb-10 d-flex align-center justify-start">
      <v-chip v-for="(info, index) in showRecruitInfoList" :key="info.recruitField" class="ma-2" closable
        @click:close="removeRecruitInfo(index)">
        {{ info.recruitField }} - {{ info.count }}명
      </v-chip>
    </v-row>

    <!-- 멤버 추가  -->

    <!-- 멤버 추가 버튼 및 모달 -->
    <!-- <v-row class="mt-10 align-center justify-start">
      <h3 style="margin-right: 20px;"> 멤버 추가 </h3>
      <ButtonComponent content="검색" @click="searchMemberShowModal()" />
    </v-row> -->

    <!-- 모달 외부에서 showMemberList의 멤버들을 Chip으로 보여줌 -->
    <!-- <v-row class="mt-10 mb-10">
      <v-chip v-for="(member, index) in showMemberList" :key="member.memberId" closable
        @click:close="removeMember(index)" class="ma-2">
        {{ member.name }} - {{ member.jobfield }}
      </v-chip>
    </v-row> -->
    <!-- 
    <v-row>
      <div id="editor"></div>
    </v-row> -->
    <v-row>
      <h3 class="mr-10 mb-5"> 프로젝트 소개 </h3>
      <v-textarea class="textareaSize" v-model="recruitContents" row-height="30" no-resize></v-textarea>
    </v-row>
    <v-row justify="center" class="mt-15 ">

      <v-col cols="auto">
        <ButtonComponent content="취소" :style="{ color: '#650101', backgroundColor: '#FFAFAF' }" @click="reloadPage()"
          class="ml-1" />
      </v-col>
      <v-col cols="auto">
        <ButtonComponent content="확인" @click="saveContent()" class="mr-1" />
      </v-col>
    </v-row>

    <!-- member add 모달창을 위한 v-dialog -->
    <!-- <v-dialog v-model="membrerAddDialog" max-width="800px" class="pa-10">
      <v-card class="modal-card">
        <v-card-title class="modal-title">멤버 추가</v-card-title>
        <v-divider class="mb-4"></v-divider>
        <v-card-text>
          <v-container class="pa-4">
            <v-row class="mt-3 mb-3 ml-9">
              <h3> 직무 </h3>
            </v-row>
            <v-row class="mb-10">
              <v-select v-model="memberField" clearable label="Field" density="compact"
                :items="['DESIGNER', 'FRONTEND', 'BACKEND', 'APP', 'PM']" variant="outlined"
                class="ml-10 mr-10"></v-select>
            </v-row>

            <v-row class="mt-10">
              <v-form @submit.prevent="searchMembersList()">
                <v-row class="mt-3 mb-3 ml-10">
                  <h3> 검색조건 </h3>
                </v-row>
                <v-row class="mt-3 mb-10">
                  <v-select v-model="searchType" :items="searchOptions" item-title="text" item-value="value"
                    variant="outlined" density="compact" class="ml-10 mr-10">
                  </v-select>
                </v-row>
                <br />
                <v-row class="mt-3 mb-3 ml-10">
                  <h3> 검색어 </h3>
                </v-row>
                <v-row class="mb-10">
                  <v-text-field v-model="searchValue" label="Search" variant="outlined" class="ml-10 mr-10"
                    density="compact"></v-text-field>
                </v-row>
                <br />

                <v-row>
                  <ButtonComponent content="검색" type="submit" class="mx-auto" @click="searchMembersList()" />
                </v-row>

                <v-row>
                  <v-col cols="12" class="mt-2">
                    <v-table rounded="lg">
                      <thead>
                        <tr>
                          <th class="text-center">선택</th>
                          <th class="text-center">이름</th>
                          <th class="text-center">EMAIL</th>
                          <th class="text-center">닉네임</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="member in memberList" :key="member.memberId" @click="selectMember(member)"
                          class="hoverable">
                          <td class="text-center">
                            <v-radio :value="member.memberId" v-model="selectedMember"></v-radio>
                          </td>
                          <td class="text-center">{{ member.name }}</td>
                          <td class="text-center">{{ member.email }}</td>
                          <td class="text-center">{{ member.nickname }}</td>
                        </tr>
                      </tbody>
                    </v-table>
                  </v-col>
                </v-row>
              </v-form>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-row justify="center">
            <ButtonComponent content="취소" :style="{ color: '#650101', backgroundColor: '#FFAFAF' }"
              @click="closeMemberAddDialog()" class="ml-1" />
            <ButtonComponent content="확인" @click="confirmMemberSelection()" />
          </v-row>
        </v-card-actions>
        <v-spacer :style="{ height: '20px' }"></v-spacer>
      </v-card>
    </v-dialog> -->

    <!-- recruitInfo 모달 창 -->
    <v-dialog v-model="recruitInfoDialogue" rounded="xl" max-width="430px" class="h-50">
      <v-card class="modal-card">
        <v-card-title class="modal-title">모집 정보 추가</v-card-title>
        <v-divider class="mb-4"></v-divider>
        <!-- 직무 선택 -->
        <v-card-text>
          <v-row class="mb-10 ml-10">
            <h3 style="margin-right: 20px;"> 직무 </h3>
            <v-select v-model="recruitField" clearable label="Field" density="compact"
              :items="['DESIGNER', 'FRONTEND', 'BACKEND', 'APP', 'PM']" variant="outlined" class="mr-10"></v-select>
          </v-row>
          <v-row class="ma-10">
            <h3 style="margin-right: 20px;"> 인원 수 </h3>
            <v-text-field v-model="count" type="number" rounded="xs" variant="outlined"></v-text-field>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-row justify="center">
            <ButtonComponent content="취소" :style="{ color: '#650101', backgroundColor: '#FFAFAF' }"
              @click="recruitInfoDialogueClose()" class="ml-1" />
            <ButtonComponent content="확인" @click="recruitInfoConfirm()" />
          </v-row>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
  <v-dialog v-model="dateModal" max-width="500px" rounded="xl">
    <v-card>
      <v-card-title>모집 마감 기한</v-card-title>
      <v-divider class="mb-4"></v-divider>
      <v-card-text>모집 마감 기한은 현재보다 이전으로 할 수 없습니다.</v-card-text>
      <v-card-actions>
        <v-row justify="center">
          <v-btn rounded="xl" variant="flat" density="default" color="#A4DEC6" :style="{ color: '#FFFFFF' }"
            @click="dateModalClose()">확인</v-btn>
        </v-row>
      </v-card-actions>
      <v-divider class="mt-2 mb-10"></v-divider>
    </v-card>
  </v-dialog>


  <v-dialog v-model="titleModal" max-width="500px" rounded="xl">
    <v-card>
      <v-card-title>제목</v-card-title>
      <v-divider class="mb-4"></v-divider>
      <v-card-text>제목을 입력하셔야 합니다.</v-card-text>
      <v-card-actions>
        <v-row justify="center">
          <v-btn rounded="xl" variant="flat" density="default" color="#A4DEC6" :style="{ color: '#FFFFFF' }"
            @click="titleModalClose()">확인</v-btn>
        </v-row>
      </v-card-actions>
      <v-divider class="mt-2 mb-10"></v-divider>
    </v-card>
  </v-dialog>
</template>
<script>
import ButtonComponent from "@/components/button/ButtonComponent.vue";
// import Editor from "@toast-ui/editor";
// import "@toast-ui/editor/dist/toastui-editor.css";
// import dayjs from "dayjs";
import axios from "axios";
import { useRoute } from 'vue-router';

export default {
  components: {
    ButtonComponent,
  },
  data() {
    return {
      dateModal: false,
      titleModal: false,
      now: null,
      recruitContents: "",
      projectImageFile: null,
      projectImageUrl: "",
      // 기존 데이터
      membrerAddDialog: false,
      searchValue: "",
      searchType: "optional",
      searchOptions: [
        { text: "이름", value: "name" },
        { text: "이메일 아이디", value: "email" },
        { text: "닉네임", value: "nickname" },
      ],
      memberField: "",
      selectedMember: null, // 현재 선택된 멤버 ID
      memberList: [], // 최종적으로 선택된 멤버들의 리스트
      showMemberList: [],

      // 모집 정보
      recruitInfoDialogue: false,
      showRecruitInfoList: [],
      recruitField: "",
      count: "",


      title: "",
      description: "",
      deadline: "",
      //editor: null,
      contents: "",
      isClosed: false,
    };
  },
  async created() {
    // const route = useRoute();
    // this.projectId = route.params.projectId;
    // const getProjectResponse = axios.get(`${process.env.VUE_APP_API_BASE_URL}/api/project/${this.projectId}`)
    // console.log(getProjectResponse);
    // this.deadline = (await getProjectResponse).data.deadline.split('T')[0];
    // this.title=(await getProjectResponse).data.projectName;
    // this.projectImageUrl=(await getProjectResponse).data.imageUrl;
    // this.description=(await getProjectResponse).data.description;
    // this.showMemberList = (await getProjectResponse).data.projectMembers.map((member) => {
    //   return {
    //     memberId: member.id,
    //     name: member.memberName, // 이름을 Chip에 표시하기 위해 추가
    //     jobfield: member.jobField, // 사용자가 선택한 직무 필드
    //   }
    // });
    // this.contents=(await getProjectResponse).data.recruitmentContents;
  },
  async mounted() {


    const route = useRoute();
    this.projectId = route.params.projectId;
    const getProjectResponse = await axios.get(`${process.env.VUE_APP_API_BASE_URL}/api/project/${this.projectId}`)
    this.deadline = getProjectResponse?.data?.deadline;
    this.title = getProjectResponse?.data?.projectName;
    this.projectImageUrl = getProjectResponse?.data?.imageUrl;
    this.description = getProjectResponse?.data?.description;
    this.recruitContents = getProjectResponse?.data?.recruitmentContents;
    this.isClosed = getProjectResponse?.data?.isClosed;




    this.showMemberList = getProjectResponse?.data?.projectMembers.map((member) => {
      return {
        memberId: member.memberId,
        name: member.memberNickname,
        jobfield: member.jobField,
      }
    });
    this.showRecruitInfoList = getProjectResponse?.data?.recruitInfos.map((info) => {
      return {
        recruitField: info.jobField,
        count: info.count
      }
    })


    this.contents = getProjectResponse?.data?.recruitmentContents;

    // this.editor = new Editor({
    //   el: document.querySelector("#editor"),
    //   height: "500px",
    //   initialEditType: "wysiwyg",
    //   initialValue:`${this.contents ?? ""}`,
    //   width: 'auto',
    //   hooks: {
    //     addImageBlobHook: async (blob, callback) => {
    //       // 1. 다른 서버에 이미지를 업로드
    //       const uploadResult = await this.uploadImage(blob);
    //       // 2. 1에서 업로드 된 이미지를 접근할 수 있는 url 세팅
    //       callback(uploadResult);
    //     },
    //   },
    // });
    // // 추가: editor의 width를 1200px로 조정
    // document.querySelector("#editor").style.width = "1200px";
  },
  methods: {
    dateModalClose() {
      this.dateModal = false;
    },
    titleModalClose() {
      this.titleModal = false;
    },

    async uploadImage(blob) {

      const accessToken = localStorage.getItem('token');
      const body = {
        prefix: "test-prefix",
        url: `${blob.name}`,
      };
      const headers = {
        Authorization: `Bearer ${accessToken}`,
        "Content-Type": "application/json", // JSON 형식의 데이터를 전송할 경우 Content-Type을 지정해야 합니다.
      };
      const getUrl = await fetch(`${process.env.VUE_APP_API_BASE_URL}/api/upload/prisigned-url`,
        {
          method: "POST",
          headers: headers,
          body: JSON.stringify(body), // body를 JSON 문자열로 변환하여 전송합니다.
        }
      );

      const urlContentType = getUrl.headers.get("content-type");
      let getUrlResult;
      if (urlContentType && urlContentType.includes("application/json")) {
        getUrlResult = await getUrl.json(); // JSON으로 파싱
      } else {
        getUrlResult = await getUrl.text(); // 텍스트로 파싱
      }

      const awsUrl = {
        data: `${getUrlResult.split("?")[0]}`,
        auth: `?${getUrlResult.split("?")[1]}`,
      };

      // // 파일을 S3에 업로드
      const options = {
        method: "PUT", // PUT 메서드 사용
        headers: {
          "Content-Type": blob.type, // Blob의 MIME 타입 설정
        },
        body: blob, // 업로드할 파일 데이터
      };
      let response = await fetch(awsUrl.data + awsUrl.auth, options);
      console.log(response);

      return awsUrl.data;
    },
    async fileUpdate(event) {
      this.projectImageFile = event.target.files[0];
      this.projectImageUrl = await this.uploadImage(this.projectImageFile);

    },

    // recruit info modal open
    recruitInfoAdd() {
      this.recruitInfoDialogue = true;
    },
    // recruit info modal close
    recruitInfoDialogueClose() {
      this.recruitInfoDialogue = false;
      this.recruitInfoClear();
    },
    // recruit confirm
    recruitInfoConfirm() {
      if (this.recruitField != "" && this.count != "") {
        this.showRecruitInfoList.push({
          recruitField: this.recruitField,
          count: this.count,
        });
        this.recruitInfoDialogueClose();
      } else {
        alert("필드와 인원 수를 모두 입력해주세요.");
      }
    },
    // recruit remove
    removeRecruitInfo(index) {
      this.showRecruitInfoList.splice(index, 1);
    },
    // recruit clear
    recruitInfoClear() {
      this.recruitField = "";
      this.count = "";
    },
    reloadPage() {
      window.history.back();
    },
    searchMemberShowModal() {
      this.membrerAddDialog = true;
    },
    closeMemberAddDialog() {
      this.membrerAddDialog = false;
      this.clearMemberAddModal();
    },
    async searchMembersList() {
      const params = {};
      if (this.searchType === "name") {
        params.name = this.searchValue;
      } else if (this.searchType === "email") {
        params.email = this.searchValue;
      } else if (this.searchType === "nickname") {
        params.nickname = this.searchValue;
      }
      try {
        const response = await axios.get(
          `${process.env.VUE_APP_API_BASE_URL}/api/member/list`,
          { params });
        this.memberList = response.data.content;
      } catch (error) {
        console.error("Error fetching members:", error);
      }
    },
    selectMember(member) {
      this.selectedMember = member.memberId; // 멤버를 선택하면 해당 멤버의 ID를 저장
    },
    clearMemberAddModal() {
      this.selectedMember = null;
      this.memberField = "";
      this.memberList = [];
    },
    confirmMemberSelection() {
      const selected = this.memberList.find(
        (member) => member.memberId === this.selectedMember
      );
      if (selected) {
        // 선택된 멤버가 showMemberList에 이미 있는지 확인
        const alreadySelected = this.showMemberList.find(
          (item) => item.memberId === selected.memberId
        );
        if (!alreadySelected) {
          this.showMemberList.push({
            memberId: selected.memberId,
            name: selected.name, // 이름을 Chip에 표시하기 위해 추가
            jobfield: this.memberField, // 사용자가 선택한 직무 필드
          });
        }
      }
      this.membrerAddDialog = false; // 모달 닫기
      this.clearMemberAddModal();
    },
    removeMember(index) {
      this.showMemberList.splice(index, 1); // showMemberList에서 해당 멤버 제거
    },
    async saveContent() {
      // const content = this.editor.getMarkdown();

      // let projectMembers = [];
      // this.showMemberList.forEach((member) => {
      //   let dataMember = {
      //     memberId: member.memberId,
      //     jobField: member.jobfield,
      //   };
      //   projectMembers.push(dataMember);

      // });

      let recruitInfos = [];
      this.showRecruitInfoList.forEach((info) => {
        let dataInfo = {
          jobField: info.recruitField,
          count: info.count,
        };
        recruitInfos.push(dataInfo);
      });
      try {
        const body = {
          imageUrl: this.projectImageUrl,
          projectName: this.title,
          deadline: this.deadline,
          description: this.description,
          recruitmentContents: this.recruitContents,
          // projectMembers,
          recruitInfos,
          isClosed: this.isClosed,
        };
        if (this.title == "") {
          this.titleModal = true;
          // alert("제목을 입력해야합니다.");
          return;
        }
        else if (this.deadline < this.now) {
          this.dateModal = true;

          // alert("모집 마감 기한은 현재보다 이후여야 합니다.")
          return;
        }
        else {
          const projectCreateResponse = await axios.put(`${process.env.VUE_APP_API_BASE_URL}/api/project/${this.projectId}/update`, body);
          const createdProjectId = projectCreateResponse?.data?.id; // 생성된 프로젝트 ID
          this.$router.push({ name: 'ProjectView', params: { projectId: createdProjectId } });
        }
      } catch (e) {
        alert(JSON.stringify(e.response));
        console.log(e);
      }
    },
  },
};
</script>
<style>
.textareaSize {

  width: 100%;
  height: 500px;
  border: none;
  resize: none !important;
}
</style>
