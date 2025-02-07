<template>
  <div :class="[styles.CONTAINER.NORMAL, 'form-padding', 'vue-form-builder']">
    <form @submit.stop.prevent="saveToLocalStorage()">
      <label for="test">Enter the name for your form</label>
      <input
        type="text"
        id="test"
        v-model="name"
        placeholder="Form name"
        class="form-control"
        style="max-width: 300px;"
        required
      /><br />
      <button class="btn btn-info mr-2">
        Save your form
      </button>
      <br />
      <br />
      <div v-if="success" class="alert alert-success">
        <strong>Success!</strong> You are going to be redirected to test your
        saved form.
      </div>
    </form>
    <hr style="border-width: 2px; color: black" />
    <button class="btn btn-info mr-2" @click="printData()">
      Get JSON Form-Data (Console / Builder)
    </button>
    <br />
    <br />
    <!-- top configuration -->
    <FormConfiguration
      :permissions="permissions"
      v-model="formData.formConfig"
    />

    <!-- form headline -->
    <div
      class="form-headline-container"
      v-show="formData.formConfig.isShowHeadline"
    >
      <h1 v-text="formData.formConfig.headline"></h1>
      <p v-text="formData.formConfig.subHeadline"></p>
    </div>

    <!-- sections of the form -->
    <SectionContainer
      v-for="sectionData in sortedSections"
      :section="sectionData"
      :rows="formData.rows"
      :controls="formData.controls"
      :key="sectionData.uniqueId"
      :permissions="permissions"
    />

    <!-- below all -->
    <AddSectionControl
      v-if="permissions.canAddSection"
      @addSectionNotify="addSection"
    />

    <!-- global stuff -->
    <GlobalSidebar :formData="formData" :permissions="permissions" />
    <GlobalModal :formData="formData" :permissions="permissions" />

    <hr />

    <p class="copyright-text" v-text="copyrightText"></p>
  </div>
</template>

<script>
import AddSectionControl from "@/views/builder/add-controls/AddSectionControl";
import { MAIN_CONSTANTS } from "@/configs";
import SectionContainer from "@/views/builder/SectionContainer";
import FormBuilderBusiness from "@/mixins/form-builder-mixins";
import FormConfiguration from "@/views/builder/FormConfiguration";
import GlobalSidebar from "@/views/builder/GlobalSidebar";
import GlobalModal from "@/views/builder/GlobalModal";
import DefaultPermission from "@/configs/roles";

export default {
  name: "FormBuilder",
  components: {
    GlobalModal,
    GlobalSidebar,
    FormConfiguration,
    SectionContainer,
    AddSectionControl,
  },
  mixins: FormBuilderBusiness,

  props: {
    info: {
      type: Object,
    },
    permissions: {
      type: Object,
      default: () => {
        return DefaultPermission;
      },
    },
  },

  data: () => ({
    formData: {
      formConfig: {},
      sections: {},
      controls: {},
      rows: {},
    },
    name: null,
    success: false,
  }),

  created() {
    if (this.info) {
      this.formData = this.info.formData;
      this.name = this.info.name;
    }
    if (this.value && typeof this.value === "object") {
      this.mapping(this.value);
    } else {
      this.createDefaultData();
    }
  },
  methods: {
    printData() {
      console.log(JSON.stringify(this.formData));
    },
    saveToLocalStorage() {
      localStorage.setItem(this.name, JSON.stringify(this.formData));
      this.success = true;
      setTimeout(
        () => this.$router.push({ name: "ExampleFormRenderer" }),
        3000
      );
    },
  },
  computed: {
    /**
     * Copyright Text
     * @returns {string}
     */
    copyrightText() {
      return MAIN_CONSTANTS.COPYRIGHT;
    },
  },
};
</script>
<style scoped>
.container {
  max-width: 720px;
}
</style>
