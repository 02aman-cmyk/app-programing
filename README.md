import streamlit as st

st.title("안녕하세요 가연이의 앱이에여! 👋")
st.markdown(
    """ 
    여기서 여러가지 작품을 보여드리게용

    **There's :rainbow[so much] you can build!**
    
    We prepared a few examples for you to get started. Just 
    click on the buttons above and discover what you can do 
    with Streamlit. 
    """
)

if st.button("Send balloons!"):
    st.balloons()

# app-programing
# Streamlit 라이브러리를 가져옴 웹 앱의 UI 구성에 사용
import streamlit as st


# --- 데이터 사전 ---
# PROVERB_DATA: 수업에 사용할 속담 데이터를 저장하는 Python 딕셔너리
PROVERB_DATA = {
    "낮말은 새가 듣고 밤말은 쥐가 듣는다": {
        "meaning": "아무리 비밀히 한 말이라도 결국 다른 사람의 귀에 들어가기 쉬우니, 항상 말조심을 해야 한다는 뜻입니다.",
        "similar_proverbs": ["벽에도 귀가 있다", "말은 아낄수록 좋다"],
        "opposite_proverbs": ["침묵은 금이다 (X, 상황이 달라 제외)", "말은 해야 맛", "백지장도 맞들면 낫다"],
    },
    "발 없는 말이 천 리 간다": {
        "meaning": "말은 발이 없어도 순식간에 멀리 퍼지므로, 항상 신중하게 말해야 한다는 뜻입니다.",
        "similar_proverbs": ["낮말은 새가 듣고 밤말은 쥐가 듣는다", "호랑이도 제 말 하면 온다"],
        "opposite_proverbs": ["입은 닫고 주머니는 열어라", "침묵은 금이다"],
    },
    "티끌 모아 태산": {
        "meaning": "아무리 작은 것이라도 모이고 모이면 나중에 큰 것이 된다는 뜻입니다.",
        "similar_proverbs": ["가랑비에 옷 젖는 줄 모른다", "낙숫물이 댓돌을 뚫는다"],
        "opposite_proverbs": ["소 잃고 외양간 고친다 (X, 의미가 달라 제외)", "돌다리도 두들겨 보고 건넌다 (X)"],
    },
    "가는 말이 고와야 오는 말이 곱다": {
        "meaning": "자기가 남에게 좋게 말해야 남도 자기에게 좋게 말한다는 뜻입니다.",
        "similar_proverbs": ["콩 심은 데 콩 나고 팥 심은 데 팥 난다", "뿌린 대로 거둔다"],
        "opposite_proverbs": ["호의가 계속되면 권리인 줄 안다", "원수는 외나무다리에서 만난다 (X)"],
    },
    "소 잃고 외양간 고친다": {
        "meaning": "이미 일을 실패한 뒤에 후회하며 대책을 세워도 소용이 없다는 뜻입니다. 미리 대비하는 것이 중요함을 가르쳐줍니다.",
        "similar_proverbs": ["호미로 막을 것을 가래로 막는다", "도둑 맞고 사립 고친다"],
        "opposite_proverbs": ["유비무환", "돌다리도 두들겨 보고 건넌다"],
    },
    "백지장도 맞들면 낫다": {
        "meaning": "아무리 쉬운 일이라도 혼자 하는 것보다 여러 사람이 힘을 합쳐 하는 것이 훨씬 좋다는 뜻입니다.",
        "similar_proverbs": ["세 사람이 꾸짖으면 호랑이도 굴에 들어간다", "서로 거들면 낫다"],
        "opposite_proverbs": ["사공이 많으면 배가 산으로 간다", "각자 도생"],
    }
}


# --- 시각화 헬퍼 함수 (안정화 버전) ---
def display_relationship_map(data, proverb):
    st.markdown("### 🗺️ 속담 관계 시각화", unsafe_allow_html=True)
    
    # CSS 스타일 정의: 노드 디자인과 전체 컨테이너 Flexbox 속성 설정
    st.markdown(
        f"""
        <style>
            .proverb-node {{
                padding: 7px 12px;
                border-radius: 10px;
                font-size: 14px;
                margin: 5px 0;
                text-align: center;
                box-shadow: 0 2px 4px rgba(0,0,0,0.1);
                word-break: keep-all;
                white-space: normal;
            }}
            .main-node {{ 
                background-color: #FFD700; 
                font-weight: bold; 
                border: 3px solid #FFA500; 
                min-height: 50px; 
                display: flex; 
                align-items: center; 
                justify-content: center;
                margin-top: 50px; /* 중앙 노드 위치 조정 */
            }}
            .similar-node {{ background-color: #D4EDDA; color: #155724; border: 1px solid #C3E6CB; }}
            .opposite-node {{ background-color: #F8D7DA; color: #721C24; border: 1px solid #F5C6CB; }}
            .relationship-header {{ 
                font-weight: bold; 
                margin-bottom: 10px;
                font-size: 1.1em;
                text-align: center;
            }}
        </style>
        """, unsafe_allow_html=True
    )

    # Streamlit columns을 사용하여 좌-중앙-우 레이아웃을 만들기
    col_sim, col_main, col_opp = st.columns([1, 1.5, 1])

    # 좌측 (유사 속담)
    with col_sim:
        st.markdown("<div class='relationship-header'>🤝 유사 속담</div>", unsafe_allow_html=True)
        # 유사 속담 노드 목록
        for p in data.get('similar_proverbs', []):
            st.markdown(f"<div class='proverb-node similar-node'>{p}</div>", unsafe_allow_html=True)
        # 빈 공간 채우기 (레이아웃 보정)
        if len(data.get('similar_proverbs', [])) < 3:
            st.markdown("<div style='height: 100px;'></div>", unsafe_allow_html=True)
            

    # 중앙 (메인 속담)
    with col_main:
        # 중앙 노드를 수직으로 중앙에 위치시키기 위해 상하 여백 조정
        st.markdown(f"<div class='proverb-node main-node'>{proverb}</div>", unsafe_allow_html=True)


    # 우측 (반대 속담)
    with col_opp:
        st.markdown("<div class='relationship-header'>⚔️ 반대 속담</div>", unsafe_allow_html=True)
        # 반대 속담 노드 목록
        for p in data.get('opposite_proverbs', []):
            st.markdown(f"<div class='proverb-node opposite-node'>{p}</div>", unsafe_allow_html=True)
        # 빈 공간 채우기 (레이아웃 보정)
        if len(data.get('opposite_proverbs', [])) < 3:
            st.markdown("<div style='height: 100px;'></div>", unsafe_allow_html=True)


# --- 핵심 헬퍼 함수: 정적 데이터 조회 ---
def handle_proverb_comparison(proverb):
    # 함수 정의: 속담을 인수로 받아 데이터를 찾아 화면에 표시
    """특정 속담의 유사/반대 속담을 정적 데이터에서 탐색하는 기능"""
    # 현재 탐색 중임을 알리는 정보 메시지를 화면에 표시
    st.info(f"📚 **'{proverb}'** 와 관련된 속담들을 사전에서 탐색합니다...")
    
    # 속담 데이터 조회: PROVERB_DATA 딕셔너리에서 입력받은 속담(proverb)의 값을 가져오기
    data = PROVERB_DATA.get(proverb)

    # 데이터가 존재하는 경우 (조회에 성공한 경우)
    if data:
        # 큰 제목을 설정 (조회된 속담명 표시)
        st.subheader(f"📖 '{proverb}' 깊이 알아보기")
        # 속담의 핵심 의미를 Markdown 형식으로 표시
        st.markdown(f"**📌 핵심 의미:** {data.get('meaning', '의미를 찾을 수 없습니다.')}")
        # 유사 속담 목록을 쉼표(,)로 연결하여 Markdown 형식으로 표시
        st.markdown(f"**🤝 유사 속담:** {', '.join(data.get('similar_proverbs', ['없음']))}")
        # 반대 속담 목록을 쉼표(,)로 연결하여 Markdown 형식으로 표시
        st.markdown(f"**⚔️ 반대 속담:** {', '.join(data.get('opposite_proverbs', ['없음']))}")
        # 조회 성공 메시지를 표시
        st.success("✅ 정적 데이터 조회가 완료되었습니다.")
        
        # --- 시각화 기능 호출: AI-TPACK의 T 요소 핵심 ---
        display_relationship_map(data, proverb)
        # ------------------------------------
    # 데이터가 존재하지 않는 경우
    else:
        # 데이터가 없음을 경고하는 메시지를 표시
        st.warning(f"오늘 배운 속담이 아닙니다. 오늘 배운 속담이라면 맞춤법을 확인해주세요.")



# 페이지의 기본 설정 (제목, 레이아웃)을 지정
st.set_page_config(page_title="속담 마스터 챗봇 (시각화)", layout="centered")

# CSS 스타일 정의: 웹 앱의 미적 요소(버튼 색상, 헤더 스타일 등)를 설정
st.markdown(
    """
    <style>
        .stButton>button {
            width: 100%;
            background-color: #007BFF; /* 버튼 배경색을 파란색으로 변경 */
            color: white;
            padding: 10px;
            border-radius: 8px;
            transition: all 0.2s;
        }
        .stButton>button:hover {
            background-color: #0056b3; /* 마우스 오버 시 색상 변경 */
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        .main-header {
            color: #333;
            text-align: center;
            font-size: 2.2rem;
            font-weight: bold;
            border-bottom: 3px solid #FFD700;
            padding-bottom: 10px;
        }
    </style>
    """, unsafe_allow_html=True # HTML/CSS 코드를 Streamlit에 삽입할 수 있도록 허용하는 것
)

# 메인 헤더 제목을 표시
st.markdown('<p class="main-header">✨ 6학년 속담 마스터 챗봇 ✨</p>', unsafe_allow_html=True)

# 세션 상태 초기화: 'proverb_input' 변수가 세션에 없으면 빈 문자열로 초기화
if 'proverb_input' not in st.session_state:
    st.session_state.proverb_input = ""


# 구분선을 표시
st.markdown("---")

# 서브 헤더를 표시
st.subheader("📚 속담 깊이 탐색하기 (관계 시각화 지원)")
# 텍스트 입력 위젯을 생성 -> 사용자가 속담을 입력하는 곳
st.session_state.proverb_input = st.text_input(
    "우리 모둠의 속담을 입력해 주세요. ", 
    key='proverb_text_comp',
    # 앱 시작 시 기본값을 빈 문자열로 설정
    value='백지장도 맞들면 낫다' # 새로운 속담으로 테스트할 수 있도록 기본값 설정
)
# 버튼 위젯을 생성
if st.button("속담 탐색 시작", key='run_comparison', disabled=not st.session_state.proverb_input):
    # 버튼이 눌리고 입력된 속담이 있을 경우, 위에서 정의한 함수를 호출
    handle_proverb_comparison(st.session_state.proverb_input)

# 구분선을 표시
st.markdown("---")
# 앱의 작동 모드에 대한 설명을 작은 글씨로 표시
st.caption("AI-TPACK의 T요소: 정적 데이터를 활용한 관계 시각화")
