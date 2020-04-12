<template>
  <div>
    <h3>{{ poll.topic }}</h3>

    <div class="poll__choice--list">
      <div
        v-for="choice in poll.choices"
        :key="choice.id"
        class="poll__choice--container"
        @click="selectChoice(choice)"
      >
        <div class="poll__choice--box" :class="selectedChoiceClass(choice)">
          <span>Select {{ choice.text }}</span>
          <span>({{ choice.count }} votes)</span>
        </div>
      </div>
    </div>

    <div v-if="selectedChoiceId > 0" class="poll__vote">
      <textarea v-model="comment" placeholder="Enter an optional comment here"></textarea>
      <button @click="voteChoice()">Vote!</button>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'nuxt-property-decorator';

import { Poll, Choice, ChoiceVote } from '@/lib/polls/models';
import { pollsModule } from '@/store/polls/const';
import axios from 'axios';
import curlirize from 'axios-curlirize'

@Component({})
export default class PollDetail extends Vue {
  /**
   * Optional comment
   */
  private comment: string = '';
  /**
   * Avoid undefined to make it reactive
   */
  private selectedChoiceId: number = -1;

  @Prop({ type: Object })
  public poll!: Poll;

  @pollsModule.Action('vote')
  private vote!: (choiceVote: ChoiceVote) => void;

  /**
   * get selected CSS class if the input choice is selected
   */
  public selectedChoiceClass(choice: Choice): string {
    return this.selectedChoiceId === choice.id ? 'selected' : '';
  }

  public selectChoice(choice: Choice): void {
    this.selectedChoiceId = choice.id;
    const map = new Map<string, string>();
    const heads = new Map<string, string>();
    heads.set('authorization', 'alskjdlnlakdsf');
    map.set('key1', 'value1');
    map.set('key2', 'value2');
    map.set('key3', 'true');
    map.set('key4', '12333');
    curlirize(axios)
    axios({
      method: "GET",
      baseURL: 'http://localhost:8080',
      url: '/static/json/data.json',
      params: this.stringMapToJson(map),
      headers: this.stringMapToJson(heads),
      data: {
        "content" : {
          "name" : "aaaa"
        },
        "entity" : 123121
      } 
    }).then(res => {
      alert(JSON.stringify(res.data))
    });
  }

  public stringMapToJson(map: Map<string, string>): Object {
    let json = '{';
    map.forEach((v, k) => {
      json += `"${k}":"${v}",`;
    });
    json = json.substr(0, json.length - 1);
    json += '}';
    return JSON.parse(json);
  }

  public voteChoice(): void {
    this.vote({
      choiceId: this.selectedChoiceId,
      comment: this.comment.length > 0 ? this.comment : undefined
    });

    // reset vote selection
    this.selectedChoiceId = -1;
    this.comment = '';
  }
}
</script>

<style lang="scss">
.poll__choice--list {
  display: flex;
  flex-flow: row wrap;
}

.poll__choice--container {
  width: 100%;
  padding: 0.5rem;
  box-sizing: border-box;
  @include gt-sm {
    width: 25%;
  }
}

.poll__choice--box {
  @extend %z-depth-1;
  background-color: $colorPrimary;
  color: #ddd;
  padding: 1rem;
  border: 1px solid $colorPrimaryDark;
  border-radius: 4px;
  transition: background-color 0.2s, color 0.2s;

  span {
    display: block;
    text-align: center;
  }
  &:hover {
    cursor: pointer;
    @extend %z-depth-2;
  }

  &.selected {
    @extend %z-depth-3;
    background-color: $colorPrimaryDark;
    color: #fff;
  }
}

.poll__vote {
  textarea {
    width: calc(100% - 1rem);
    margin: 0px 0.5rem;
    padding: 0.5rem;
    box-sizing: border-box;
    resize: vertical;
  }
  button {
    display: block;
    margin: 1rem auto;
    width: 50%;
    max-width: 300px;
    padding: 8px 0px;
    background-color: $colorSecondary;
    color: #ddd;
    border: 1px solid $colorSecondaryDark;
    border-radius: 4px;
    transition: background-color 0.2s, color 0.2s;

    &:hover {
      cursor: pointer;
      background-color: $colorSecondaryDark;
      color: #fff;
    }
  }
}
</style>
