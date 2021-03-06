<template>
  <widget-editor :navItems="navItems">
    <!-- streamboss setup -->
    <div slot="goal-properties" key="goal-properties">
      <div v-if="hasGoal">
        <div class="section__body">
          <v-form-group :title="$t('Current Boss Name')">{{ wData.goal.boss_name }}</v-form-group>
          <v-form-group :title="$t('Total Health')">{{ wData.goal.total_health }}</v-form-group>
          <v-form-group :title="$t('Current Health')">{{ wData.goal.current_health }}</v-form-group>
          <v-form-group :title="$t('Mode')">{{ wData.goal.mode }}</v-form-group>
          <button class="button button--warn" @click="resetGoal()">
            {{ $t('Reset Stream Boss') }}
          </button>
        </div>
      </div>

      <div v-else>
        <validated-form ref="form" class="section__body" v-if="requestState !== 'pending'">
          <v-form-group
            v-model="bossCreateOptions.total_health"
            :metadata="metadata.total_health"
          />
          <v-form-group v-model="bossCreateOptions.mode" :metadata="metadata.mode" />
          <validated-form v-if="loaded" @input="save()">
            <v-form-group
              v-if="bossCreateOptions.mode === 'incremental'"
              v-model="wData.settings.incr_amount"
              :metadata="metadata.incr_amount"
            />
            <v-form-group
              v-if="bossCreateOptions.mode === 'overkill'"
              v-model="wData.settings.overkill_multiplier"
              :metadata="metadata.overkill_multiplier"
            />
            <v-form-group
              v-if="bossCreateOptions.mode === 'overkill'"
              v-model="wData.settings.overkill_min"
              :metadata="metadata.overkill_min"
            />
          </validated-form>
        </validated-form>
        <div v-else class="loading-spinner">
          <img src="../../../media/images/loader.svg" />
        </div>
        <button @click="saveGoal()" class="button button--action">
          {{ $t('Set Stream Boss Health') }}
        </button>
      </div>
    </div>

    <validated-form
      slot="manage-battle-properties"
      key="manage-battle-properties"
      name="manage-battle-form"
      @input="save()"
      v-if="loaded"
    >
      <v-form-group v-model="wData.settings.fade_time" :metadata="metadata.fade_time" />
      <v-form-group>
        <bool-input v-model="wData.settings.boss_heal" :metadata="metadata.boss_heal" />
      </v-form-group>
      <v-form-group v-model="wData.settings.skin" :metadata="metadata.skin" />
      <v-form-group
        v-for="multiplier in multipliersForPlatform"
        :key="multiplier.key"
        v-model="wData.settings[multiplier.key]"
        type="number"
        :metadata="multiplier"
      />
    </validated-form>

    <validated-form
      slot="visual-properties"
      key="visual-properties"
      @input="save()"
      name="visual-settings-form"
      v-if="loaded"
    >
      <v-form-group v-model="wData.settings.kill_animation" :metadata="metadata.kill_animation" />
      <v-form-group>
        <bool-input v-model="wData.settings.bg_transparent" :metadata="metadata.bg_transparent" />
      </v-form-group>
      <v-form-group
        v-model="wData.settings.background_color"
        :metadata="metadata.background_color"
      />
      <v-form-group v-model="wData.settings.text_color" :metadata="metadata.text_color" />
      <v-form-group v-model="wData.settings.bar_text_color" :metadata="metadata.bar_text_color" />
      <v-form-group v-model="wData.settings.bar_color" :metadata="metadata.bar_color" />
      <v-form-group v-model="wData.settings.bar_bg_color" :metadata="metadata.bar_bg_color" />
      <v-form-group v-model="wData.settings.font" :metadata="metadata.font" />
    </validated-form>
  </widget-editor>
</template>

<script lang="ts" src="./StreamBoss.vue.ts"></script>

<style lang="less" scoped>
.loading-spinner {
  position: relative;
  width: 100%;
  height: 100%;
  padding-top: 100px;

  img {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 0);
  }
}
</style>
