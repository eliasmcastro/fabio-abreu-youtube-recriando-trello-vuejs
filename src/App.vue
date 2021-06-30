<template>
  <div id="app">
    <Header/>
    <div class="board">
      <div class="lane">
        <h2 class="lane-title">Backlog</h2>
        <Container 
          group-name="trello" 
          @drag-start="handleDragStart('backlog', $event)"
          @drop="handleDrop('backlog', $event)" 
          :get-child-payload="getChildPayLoad"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.backlog" v-bind:key="card.id">
            <Card>
              {{ card.text }}
            </Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">Desenvolvimento</h2>
        <Container 
          group-name="trello" 
          @drag-start="handleDragStart('dev', $event)" 
          @drop="handleDrop('dev', $event)" 
          :get-child-payload="getChildPayLoad"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.dev" v-bind:key="card.id">
            <Card>
              {{ card.text }}
            </Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">Testes</h2>
        <Container 
          group-name="trello" 
          @drag-start="handleDragStart('tests', $event)" 
          @drop="handleDrop('tests', $event)" 
          :get-child-payload="getChildPayLoad"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.tests" v-bind:key="card.id">
            <Card>
              {{ card.text }}
            </Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">Fechados</h2>
        <Container 
          group-name="trello" 
          @drag-start="handleDragStart('closed', $event)" 
          @drop="handleDrop('closed', $event)" 
          :get-child-payload="getChildPayLoad"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.closed" v-bind:key="card.id">
            <Card>
              {{ card.text }}
            </Card>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Card from './components/Card.vue'
import initialCards from './initialCards'
import {Container, Draggable} from 'vue-smooth-dnd'

export default {
  name: 'App',

  components: {
    Header,
    Card,
    Container,
    Draggable
  },

  data() {
    return {
      cards: {
        backlog: initialCards.backlog,
        dev: initialCards.dev,
        tests: initialCards.tests,
        closed: []
      },
      draggingCard: {
        lane: '',
        index: -1,
        cardData: {}
      }
    }
  },

  methods: {
    handleDragStart(lane, dragResult) {
      const { payload, isSource } = dragResult
      
      if (isSource) {
        this.draggingCard = {
          lane,
          index: payload.index,
          cardData: {
            ...this.cards[lane][payload.index]
          }
        }
      }
    },

    handleDrop(lane, dropResult) {
      const { removedIndex, addedIndex } = dropResult

      if (this.draggingCard.lane === lane && removedIndex === addedIndex) {
        return
      }

      if (removedIndex !== null) {
        this.cards[lane].splice(removedIndex, 1)
      }

      if (addedIndex !== null) {
        this.cards[lane].splice(addedIndex, 0, this.draggingCard.cardData)
      }
    },

    getChildPayLoad(index) {
      return {
        index
      }
    }
  }
}
</script>

<style>
.board {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  margin: 1.2rem 0.8rem;
}

.lane {
  width: 23rem;
  background: var(--color-grey);
  border-radius: 0.8rem;
  box-shadow: 0 0.1rem 0.2rem 0 rgba(33, 33, 33, 0.1);
  margin: 0 0.8rem;
  padding: 0 0.7rem;
}

.lane-title {
  padding: 0.8rem 0.5rem;
  margin-bottom: 0.6rem;
}

.placeholder {
  background: rgba(33, 33, 33, 0.08);
  border-radius: 0.4rem;
  transform: scaleY(0.85);
  transform-origin: 0% 0%;
}
</style>