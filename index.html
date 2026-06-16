import streamlit as st
import pandas as pd
import sqlite3
import hashlib
from datetime import datetime, date, timedelta

# ==========================================
# 1. 页面配置与全局样式
# ==========================================
st.set_page_config(
    page_title="LifeExpiry - 个人物资守护官",
    page_icon="🏠",
    layout="wide",
    initial_sidebar_state="expanded"
)

st.markdown("""
<style>
    .metric-card {
        background-color: #f8f9fa;
        padding: 20px;
        border-radius: 10px;
        border-left: 5px solid #6c757d;
        box-shadow: 2px 2px 5px rgba(0,0,0,0.05);
    }
    .card-red { border-left: 6px solid #ff4b4b; background-color: #ffebeb; }
    .card-yellow { border-left: 6px solid #ffa500; background-color: #fff5e6; }
    .card-green { border-left: 6px solid #28a745; background-color: #f4fbf5; }
    
    .item-title { font-size: 1.1rem; font-weight: bold; color: #31333F; margin-bottom: 5px; }
    .item-details { font-size: 0.85rem; color: #555; line-height: 1.4; }
    .status-badge {
        float: right; padding: 2px 8px; border-radius: 12px; font-size: 0.75rem; font-weight: bold; color: white;
    }
    .badge-red { background-color: #ff4b4b; }
    .badge-yellow { background-color: #ffa500; color: #333; }
    .badge-green { background-color: #28a745; }
    
    .login-container {
        max-width: 400px;
        margin: 10% auto;
        padding: 2rem;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.1);
    }
</style>
""", unsafe_allow_html=True)

# ==========================================
# 2. 数据库与用户认证模块
# ==========================================
DB_NAME = "life_expiry.db"

def init_db():
    with sqlite3.connect(DB_NAME) as conn:
        cursor = conn.cursor()
        cursor.execute("""
            CREATE TABLE IF NOT EXISTS users (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                username TEXT UNIQUE NOT NULL,
                password_hash TEXT NOT NULL,
                created_at TEXT NOT NULL
            )
        """)
        cursor.execute("""
            CREATE TABLE IF NOT EXISTS inventory (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                user_id INTEGER NOT NULL,
                name TEXT NOT NULL,
                category TEXT NOT NULL,
                expiry_date TEXT NOT NULL,
                add_date TEXT NOT NULL,
                FOREIGN KEY (user_id) REFERENCES users (id)
            )
        """)
        conn.commit()

def hash_password(password: str) -> str:
    return hashlib.sha256(password.encode()).hexdigest()

def add_user(username: str, password: str) -> bool:
    try:
        with sqlite3.connect(DB_NAME) as conn:
            cursor = conn.cursor()
            cursor.execute(
                "INSERT INTO users (username, password_hash, created_at) VALUES (?, ?, ?)",
                (username, hash_password(password), date.today().isoformat())
            )
            conn.commit()
        return True
    except sqlite3.IntegrityError:
        return False

def authenticate_user(username: str, password: str):
    with sqlite3.connect(DB_NAME) as conn:
        cursor = conn.cursor()
        cursor.execute(
            "SELECT id, password_hash FROM users WHERE username = ?",
            (username,)
        )
        row = cursor.fetchone()
        if row and row[1] == hash_password(password):
            return row[0]
    return None

def get_user_items(user_id: int):
    with sqlite3.connect(DB_NAME) as conn:
        df = pd.read_sql_query(
            "SELECT * FROM inventory WHERE user_id = ?",
            conn,
            params=(user_id,)
        )
    if df.empty:
        return pd.DataFrame(columns=['id', 'user_id', 'name', 'category', 'expiry_date', 'add_date'])
    return df

def add_item(user_id: int, name: str, category: str, expiry_date):
    with sqlite3.connect(DB_NAME) as conn:
        cursor = conn.cursor()
        today_str = date.today().strftime("%Y-%m-%d")
        cursor.execute(
            "INSERT INTO inventory (user_id, name, category, expiry_date, add_date) VALUES (?, ?, ?, ?, ?)",
            (user_id, name, category, expiry_date.strftime("%Y-%m-%d"), today_str)
        )

def restore_item(user_id: int, name: str, category: str, expiry_date: str, add_date: str):
    with sqlite3.connect(DB_NAME) as conn:
        cursor = conn.cursor()
        cursor.execute(
            "INSERT INTO inventory (user_id, name, category, expiry_date, add_date) VALUES (?, ?, ?, ?, ?)",
            (user_id, name, category, expiry_date, add_date)
        )

def delete_item(item_id: int, user_id: int):
    with sqlite3.connect(DB_NAME) as conn:
        cursor = conn.cursor()
        cursor.execute(
            "DELETE FROM inventory WHERE id = ? AND user_id = ?",
            (item_id, user_id)
        )

def batch_delete_items(item_ids: list, user_id: int):
    for iid in item_ids:
        delete_item(iid, user_id)

def delete_expired_items(df: pd.DataFrame, user_id: int):
    danger_ids = df[df['status_order'].isin([0, 1])]['id'].tolist()
    batch_delete_items(danger_ids, user_id)

def load_user_data(user_id: int):
    df = get_user_items(user_id)
    if df.empty:
        return pd.DataFrame(columns=['id', 'name', 'category', 'expiry_date', 'add_date', 'days_left', 'status', 'status_order'])
    
    df['expiry_date_dt'] = pd.to_datetime(df['expiry_date']).dt.date
    today = date.today()
    df['days_left'] = df['expiry_date_dt'].apply(lambda x: (x - today).days)
    
    def get_status(row):
        if row['days_left'] <= 0:
            return '🔴 已过期', 0
        elif row['days_left'] <= 1:
            return '🔴 24H内过期', 1
        
        yellow_threshold = 3
        if row['category'] == '🍓 新鲜果蔬':
            yellow_threshold = 1
        elif row['category'] == '🍿 零食饮料':
            yellow_threshold = 3
        elif row['category'] == '💊 医药急救':
            yellow_threshold = 7
        
        if row['days_left'] <= yellow_threshold:
            return '🟡 临期预警', 2
        else:
            return '🟢 安全囤货', 3

    status_info = df.apply(get_status, axis=1)
    df['status'] = [info[0] for info in status_info]
    df['status_order'] = [info[1] for info in status_info]
    
    df = df.sort_values(by=['status_order', 'days_left'], ascending=[True, True])
    return df

# ==========================================
# 3. 登录/注册 UI
# ==========================================
def login_signup_page():
    with st.container():
        col1, col2, col3 = st.columns([1, 2, 1])
        with col2:
            st.image("https://emojis.slackmojis.com/emojis/images/1643514738/13717/house.png", width=80)
            st.title("🏠 LifeExpiry")
            st.markdown("### 个人物资守护官")
            
            tab1, tab2 = st.tabs(["🔐 登录", "📝 注册"])
            
            with tab1:
                with st.form("login_form"):
                    username = st.text_input("用户名")
                    password = st.text_input("密码", type="password")
                    submitted = st.form_submit_button("登录", use_container_width=True)
                    if submitted:
                        if not username or not password:
                            st.error("请填写用户名和密码")
                        else:
                            user_id = authenticate_user(username, password)
                            if user_id:
                                st.session_state['logged_in'] = True
                                st.session_state['user_id'] = user_id
                                st.session_state['username'] = username
                                st.rerun()
                            else:
                                st.error("用户名或密码错误")
            
            with tab2:
                with st.form("reg_form"):
                    new_user = st.text_input("用户名")
                    new_pass = st.text_input("密码", type="password")
                    confirm_pass = st.text_input("确认密码", type="password")
                    submitted_reg = st.form_submit_button("注册", use_container_width=True)
                    if submitted_reg:
                        if not new_user or not new_pass:
                            st.error("用户名和密码不能为空")
                        elif new_pass != confirm_pass:
                            st.error("两次密码不一致")
                        else:
                            if add_user(new_user, new_pass):
                                st.success("注册成功！请登录")
                                st.rerun()
                            else:
                                st.error("用户名已存在")
            
            st.caption("✨ 你的物资数据只属于你自己，安全隔离存储")

# ==========================================
# 4. 撤销功能辅助函数
# ==========================================
def push_to_undo_stack(deleted_items_info):
    if 'undo_stack' not in st.session_state:
        st.session_state.undo_stack = []
    MAX_UNDO = 5
    st.session_state.undo_stack.append(deleted_items_info)
    if len(st.session_state.undo_stack) > MAX_UNDO:
        st.session_state.undo_stack.pop(0)

def undo_last_delete(user_id: int):
    if 'undo_stack' not in st.session_state or not st.session_state.undo_stack:
        st.warning("没有可撤销的操作")
        return False
    deleted_items = st.session_state.undo_stack.pop()
    for item in deleted_items:
        restore_item(
            user_id,
            item['name'],
            item['category'],
            item['expiry_date'],
            item['add_date']
        )
    return True

# ==========================================
# 5. 主应用（登录后）
# ==========================================
def refresh_user_data():
    if 'user_id' in st.session_state:
        st.session_state['df_items'] = load_user_data(st.session_state['user_id'])

def delete_item_with_undo(item_id: int, user_id: int, current_df: pd.DataFrame):
    row = current_df[current_df['id'] == item_id]
    if row.empty:
        return
    deleted_info = [{
        'name': row.iloc[0]['name'],
        'category': row.iloc[0]['category'],
        'expiry_date': row.iloc[0]['expiry_date'],
        'add_date': row.iloc[0]['add_date']
    }]
    push_to_undo_stack(deleted_info)
    delete_item(item_id, user_id)

def batch_delete_with_undo(item_ids: list, user_id: int, current_df: pd.DataFrame):
    deleted_infos = []
    for iid in item_ids:
        row = current_df[current_df['id'] == iid]
        if not row.empty:
            deleted_infos.append({
                'name': row.iloc[0]['name'],
                'category': row.iloc[0]['category'],
                'expiry_date': row.iloc[0]['expiry_date'],
                'add_date': row.iloc[0]['add_date']
            })
    if deleted_infos:
        push_to_undo_stack(deleted_infos)
        batch_delete_items(item_ids, user_id)

def main_app():
    if 'undo_stack' not in st.session_state:
        st.session_state.undo_stack = []
    
    # 侧边栏：用户信息 & 操作按钮 & 入库面板
    with st.sidebar:
        st.header(f"👋 欢迎，{st.session_state['username']}")
        
        # 退出登录按钮
        if st.button("🚪 退出登录", use_container_width=True):
            for key in ['logged_in', 'user_id', 'username', 'df_items', 'undo_stack']:
                if key in st.session_state:
                    del st.session_state[key]
            st.rerun()
        
        # 撤销上一步按钮（放在退出登录下方）
        if st.button("↩️ 撤销上一步", use_container_width=True):
            success = undo_last_delete(st.session_state['user_id'])
            if success:
                st.success("已恢复最近删除的物品")
                refresh_user_data()
                st.rerun()
            else:
                st.info("没有可撤销的操作")
        
        st.markdown("---")
        st.header("🚀 智能入库面板")
        st.caption("全屋物资一键登记，自动推算保质期")
        
        CATEGORY_SUGGESTIONS = {
            "🍿 零食饮料": 180,
            "💊 医药急救": 365,
            "🍓 新鲜果蔬": 3,
            "💄 美妆护肤": 90,
            "🏠 日用杂货": 365
        }
        
        with st.form("add_form", clear_on_submit=True):
            item_name = st.text_input("📦 物资名称", placeholder="例如：鲜牛奶、感冒药")
            item_cat = st.selectbox("🏷️ 标签化归类", options=list(CATEGORY_SUGGESTIONS.keys()))
            suggested_days = CATEGORY_SUGGESTIONS[item_cat]
            st.info(f"💡 建议：该品类常规保质期约 {suggested_days} 天")
            
            date_type = st.radio("录入方式", ["按过期日期录入", "按生产日期 + 推荐保质期"])
            if date_type == "按过期日期录入":
                exp_date = st.date_input("📅 过期日期", value=date.today() + timedelta(days=suggested_days))
            else:
                mfg_date = st.date_input("🏭 生产日期", value=date.today())
                shelf_life = st.number_input("⏳ 保质期(天)", min_value=1, value=suggested_days)
                exp_date = mfg_date + timedelta(days=int(shelf_life))
                st.caption(f"自动推算过期日：{exp_date}")
            
            submit_btn = st.form_submit_button("➕ 登记入库")
            if submit_btn:
                if item_name.strip() == "":
                    st.error("请输入物资名称！")
                else:
                    add_item(st.session_state['user_id'], item_name, item_cat, exp_date)
                    st.balloons()
                    st.success(f"🎉 {item_name} 已成功记入仓库！")
                    refresh_user_data()
        
        st.markdown("---")
        st.subheader("📤 数据备份")
        df_items = st.session_state.get('df_items', pd.DataFrame())
        if not df_items.empty:
            export_df = df_items[['name', 'category', 'expiry_date', 'days_left', 'status', 'add_date']].copy()
            export_df.rename(columns={
                'name': '物资名称',
                'category': '分类',
                'expiry_date': '过期日期',
                'days_left': '剩余天数',
                'status': '状态',
                'add_date': '添加日期'
            }, inplace=True)
            csv = export_df.to_csv(index=False, encoding='utf-8-sig')
            st.download_button(
                label="📥 导出全部物资 (CSV)",
                data=csv,
                file_name=f"lifeexpiry_{st.session_state['username']}_{date.today()}.csv",
                mime="text/csv",
                use_container_width=True
            )
        else:
            st.info("暂无数据可导出")
    
    # 主内容区域
    st.title("🏠 LifeExpiry - 全屋物资临期守护官")
    st.markdown("---")
    
    df_items = st.session_state['df_items']
    
    # 指标卡片
    expired_count = len(df_items[df_items['status_order'].isin([0, 1])]) if not df_items.empty else 0
    warning_count = len(df_items[df_items['status_order'] == 2]) if not df_items.empty else 0
    safe_count = len(df_items[df_items['status_order'] == 3]) if not df_items.empty else 0
    
    col1, col2, col3 = st.columns(3)
    with col1:
        st.markdown(f"""
        <div class="metric-card card-red">
            <h3 style='margin:0;color:#ff4b4b;'>🚨 已过期 / 24H内</h3>
            <p style='font-size:2rem; font-weight:bold; margin:10px 0 0 0;'>{expired_count} <span style='font-size:1rem;color:#666;'>件</span></p>
        </div>
        """, unsafe_allow_html=True)
    with col2:
        st.markdown(f"""
        <div class="metric-card card-yellow">
            <h3 style='margin:0;color:#ffa500;'>⏳ 临期抢救中</h3>
            <p style='font-size:2rem; font-weight:bold; margin:10px 0 0 0;'>{warning_count} <span style='font-size:1rem;color:#666;'>件</span></p>
        </div>
        """, unsafe_allow_html=True)
    with col3:
        st.markdown(f"""
        <div class="metric-card card-green">
            <h3 style='margin:0;color:#28a745;'>🛡️ 安全囤货量</h3>
            <p style='font-size:2rem; font-weight:bold; margin:10px 0 0 0;'>{safe_count} <span style='font-size:1rem;color:#666;'>件</span></p>
        </div>
        """, unsafe_allow_html=True)
    
    # 紧急通知
    if not df_items.empty:
        danger_items = df_items[df_items['status_order'].isin([0, 1, 2])]
        if not danger_items.empty:
            top_danger = danger_items.head(3)
            danger_names = "、".join([f"**{row['name']}** ({row['status']})" for _, row in top_danger.iterrows()])
            st.error(f"⚡ **今日紧急销毁清单**：您的 {danger_names} 状态告急，请优先消灭或处理！")
    
    # 统计图表
    st.subheader("📊 物资健康度与分类透视")
    if not df_items.empty:
        df_counts = df_items['category'].value_counts().reset_index()
        df_counts.columns = ['物资分类', '数量']
        chart_data = df_counts.set_index('物资分类')
        st.bar_chart(chart_data, color="#ffaa00", y="数量", use_container_width=True)
        st.caption("💡 柱状图展示了当前各分类的囤货数量透视。")
    else:
        st.info("💡 暂无库存数据，请在左侧登记物资！")
    
    st.markdown("---")
    
    # 卡片渲染函数
    def render_card_flow(data_frame, tab_name="all"):
        if data_frame.empty:
            st.info("📭 该舱位空空如也，安全感满满！")
            return
        
        cols = st.columns(3)
        for index, (_, row) in enumerate(data_frame.iterrows()):
            col = cols[index % 3]
            card_class = "card-green"
            badge_class = "badge-green"
            if row['status_order'] in [0, 1]:
                card_class = "card-red"
                badge_class = "badge-red"
            elif row['status_order'] == 2:
                card_class = "card-yellow"
                badge_class = "badge-yellow"
                
            with col:
                with st.container():
                    st.markdown(f"""
                    <div class="metric-card {card_class}">
                        <span class="status-badge {badge_class}">{row['status']}</span>
                        <div class="item-title">{row['name']}</div>
                        <div class="item-details">
                            <b>分类:</b> {row['category']}<br>
                            <b>剩余天数:</b> <span style='font-size:1.1rem;font-weight:bold;'>{row['days_left']}</span> 天<br>
                            <b>大限日期:</b> {row['expiry_date']}
                        </div>
                    </div>
                    """, unsafe_allow_html=True)
                    
                    if st.button(f"🗑️ 消耗/下架", key=f"{tab_name}_del_{row['id']}_{st.session_state['user_id']}"):
                        delete_item_with_undo(row['id'], st.session_state['user_id'], st.session_state['df_items'])
                        st.toast(f"已下架: {row['name']}")
                        refresh_user_data()
                        st.rerun()
                st.write("")
    
    # Tabs
    tab_all, tab_snack, tab_med, tab_fresh, tab_other = st.tabs([
        "📑 全部物资看板", "🍿 零食与吨吨吨", "💊 家庭药箱", "🍓 生鲜果蔬", "🏠 日用及其他"
    ])
    
    with tab_all:
        st.caption("💡 默认按紧急程度排序，已过期及医药类风险项会自动置顶")
        search_term = st.text_input("🔍 按物资名称搜索", placeholder="输入关键字...")
        if search_term:
            filtered_df = df_items[df_items['name'].str.contains(search_term, case=False, na=False)]
            if filtered_df.empty:
                st.warning("没有找到匹配的物资，请尝试其他关键词。")
        else:
            filtered_df = df_items
        
        st.markdown(f"**共找到 {len(filtered_df)} 件物资**")
        
        with st.expander("📦 批量管理物资 (勾选后批量删除)", expanded=False):
            if not filtered_df.empty:
                options = [f"{row['id']}|{row['name']} ({row['category']}) - 剩余{row['days_left']}天" for _, row in filtered_df.iterrows()]
                selected = st.multiselect("选择要删除的物资", options=options)
                col_btn1, col_btn2 = st.columns(2)
                with col_btn1:
                    if st.button("🗑️ 删除选中物资", use_container_width=True):
                        if selected:
                            ids_to_delete = [int(opt.split("|")[0]) for opt in selected]
                            batch_delete_with_undo(ids_to_delete, st.session_state['user_id'], st.session_state['df_items'])
                            st.success(f"已删除 {len(ids_to_delete)} 件物资")
                            refresh_user_data()
                            st.rerun()
                        else:
                            st.warning("请先选择物资")
                with col_btn2:
                    if st.button("🔥 一键清理已过期/24H内物资", use_container_width=True, type="primary"):
                        expired_ids = df_items[df_items['status_order'].isin([0, 1])]['id'].tolist()
                        if expired_ids:
                            batch_delete_with_undo(expired_ids, st.session_state['user_id'], st.session_state['df_items'])
                            st.success(f"已清理 {len(expired_ids)} 件过期/紧急物资")
                            refresh_user_data()
                            st.rerun()
                        else:
                            st.info("当前没有需要清理的过期物资")
            else:
                st.info("没有物资可批量管理")
        
        render_card_flow(filtered_df, tab_name="all")
    
    with tab_snack:
        render_card_flow(df_items[df_items['category'] == "🍿 零食饮料"], tab_name="snack")
    with tab_med:
        st.caption("🚨 药箱重点看护：事关生命健康，过期药品请及时下架销毁。")
        render_card_flow(df_items[df_items['category'] == "💊 医药急救"], tab_name="med")
    with tab_fresh:
        render_card_flow(df_items[df_items['category'] == "🍓 新鲜果蔬"], tab_name="fresh")
    with tab_other:
        render_card_flow(df_items[df_items['category'].isin(["💄 美妆护肤", "🏠 日用杂货"])], tab_name="other")

# ==========================================
# 6. 主入口
# ==========================================
def main():
    init_db()
    if 'logged_in' not in st.session_state:
        st.session_state['logged_in'] = False
    
    if st.session_state['logged_in']:
        if 'df_items' not in st.session_state:
            st.session_state['df_items'] = load_user_data(st.session_state['user_id'])
        main_app()
    else:
        login_signup_page()

if __name__ == "__main__":
    main()