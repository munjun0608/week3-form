<script setup>
import { ref } from "vue";

// 1. 검색 관련 상태 변수
const searchInput = ref(""); // 사용자가 입력 중인 글자
const searchKeyword = ref(""); // 엔터를 쳤을 때 확정된 검색어
const currentTab = ref("전체"); // 현재 선택된 탭

// 2. 모달창 상태 변수
const isModalOpen = ref(false);
const selectedMovie = ref(null);

// 3. 영화 배열 데이터
const movies = ref([
  {
    id: 1,
    title: "다크 나이트",
    genre: "액션",
    rating: 9.5,
    poster: "https://placehold.co/150x220/222222/FFFFFF?text=Dark+Knight",
  },
  {
    id: 2,
    title: "인터스텔라",
    genre: "SF",
    rating: 8.6,
    poster: "https://placehold.co/150x220/000080/FFFFFF?text=Interstellar",
  },
  {
    id: 3,
    title: "어바웃 타임",
    genre: "로맨스",
    rating: 7.5,
    poster: "https://placehold.co/150x220/FFB6C1/FB0000?text=About+Time",
  },
  {
    id: 4,
    title: "인셉션",
    genre: "SF",
    rating: 9.2,
    poster: "https://placehold.co/150x220/808080/FFFFFF?text=Inception",
  },
  {
    id: 5,
    title: "라라랜드",
    genre: "로맨스",
    rating: 6.8,
    poster: "https://placehold.co/150x220/FFFF00/000000?text=La+La+Land",
  },
]);

// 4. 모달창 열기 함수
const openModal = (movie) => {
  selectedMovie.value = movie;
  isModalOpen.value = true;
};

// 5. 삭제 함수
const deleteMovie = (targetId) => {
  if (confirm("정말 삭제하시겠습니까?")) {
    movies.value = movies.value.filter((movie) => movie.id !== targetId);
    isModalOpen.value = false;
  }
};
</script>

<template>
  <div class="container">
    <h1>🎬 영화 데이터베이스</h1>

    <!-- 검색 영역 -->
    <div class="search-area">
      <input
        type="text"
        v-model="searchInput"
        @keyup.enter="searchKeyword = searchInput"
        placeholder="영화 제목 검색 후 엔터"
      />
      <button @click="searchKeyword = searchInput">검색</button>
    </div>

    <!-- 탭 메뉴 (카테고리 필터) -->
    <div class="tabs">
      <button
        v-for="tab in ['전체', '액션', 'SF', '로맨스']"
        :key="tab"
        @click="currentTab = tab"
        :class="{ active: currentTab === tab }"
      >
        {{ tab }}
      </button>
    </div>

    <!-- 영화 그리드 리스트 -->
    <div class="movie-grid">
      <div
        v-for="movie in movies"
        :key="movie.id"
        class="card"
        v-show="
          (currentTab === '전체' || movie.genre === currentTab) &&
          movie.title.includes(searchKeyword)
        "
      >
        <img :src="movie.poster" alt="포스터" />
        <h3>{{ movie.title }}</h3>
        <p>⭐ {{ movie.rating }} / {{ movie.genre }}</p>

        <!-- 평점 배지 -->
        <div class="badge-area">
          <span v-if="movie.rating >= 9" class="badge master">👑 명작</span>
          <span v-else-if="movie.rating >= 7" class="badge good">👍 추천</span>
          <span v-else class="badge soso">🤔 킬링타임</span>
        </div>

        <button class="detail-btn" @click="openModal(movie)">상세보기</button>
      </div>
    </div>

    <!-- 상세 모달창 -->
    <div v-if="isModalOpen" class="modal-bg" @click="isModalOpen = false">
      <div class="modal-content" @click.stop>
        <h2>{{ selectedMovie.title }} 상세정보</h2>
        <img :src="selectedMovie.poster" alt="포스터" />
        <div class="modal-info">
          <p><strong>장르:</strong> {{ selectedMovie.genre }}</p>
          <p><strong>평점:</strong> ⭐ {{ selectedMovie.rating }}</p>
        </div>
        <div class="modal-actions">
          <button class="close-btn" @click="isModalOpen = false">닫기</button>
          <button class="delete-btn" @click="deleteMovie(selectedMovie.id)">
            🗑️ 삭제하기
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
  font-family: "Pretendard", sans-serif;
}

/* 검색창 */
.search-area {
  margin-bottom: 20px;
}
.search-area input {
  padding: 10px;
  width: 260px;
  border: 1px solid #ccc;
  border-radius: 6px;
  margin-right: 8px;
  font-size: 15px;
}
.search-area button {
  padding: 10px 20px;
  background-color: #333;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
}

/* 탭 스타일 */
.tabs {
  margin-bottom: 30px;
  display: flex;
  justify-content: center;
  gap: 10px;
}
.tabs button {
  padding: 10px 20px;
  border: 1px solid #ddd;
  background: #f9f9f9;
  cursor: pointer;
  border-radius: 8px;
  transition: 0.2s;
}
.tabs button.active {
  background: #422883;
  color: white;
  border-color: #422883;
  font-weight: bold;
}

/* 카드 스타일 */
.movie-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 30px;
  justify-content: center;
}
.card {
  border: 1px solid #eee;
  padding: 20px;
  border-radius: 12px;
  width: 220px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
  background: white;
}
.card img {
  width: 100%;
  border-radius: 8px;
  margin-bottom: 10px;
}

/* 배지 */
.badge-area {
  margin: 15px 0;
}
.badge {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: bold;
}
.badge.master {
  background-color: #fff3cd;
  color: #856404;
  border: 1px solid #ffeeba;
}
.badge.good {
  background-color: #d1ecf1;
  color: #0c5460;
  border: 1px solid #bee5eb;
}
.badge.soso {
  background-color: #f8f9fa;
  color: #6c757d;
  border: 1px solid #dee2e6;
}

/* 상세보기 버튼 */
.detail-btn {
  width: 100%;
  padding: 10px;
  background: #4bb633;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
}

/* 모달 스타일 */
.modal-bg {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.modal-content {
  background: white;
  padding: 30px;
  border-radius: 16px;
  width: 320px;
  position: relative;
}
.modal-content img {
  width: 100%;
  max-width: 180px;
  border-radius: 8px;
  margin-bottom: 15px;
}
.modal-info {
  text-align: left;
  margin-bottom: 20px;
}
.modal-actions {
  display: flex;
  gap: 10px;
  justify-content: center;
}
.modal-actions button {
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  border: none;
  font-weight: bold;
}
.close-btn {
  background: #eee;
  color: #333;
}
.delete-btn {
  background: #ff4d4d;
  color: white;
}
</style>
