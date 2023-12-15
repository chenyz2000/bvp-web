<template>
    <!-- 列表 -->
    <div v-show="!playVideo">
        <!-- 对列表进行条件筛选 -->
        <div>
            <el-input
                v-model="inputKeywords"
                style="width: 200px"
                placeholder="关键词"
                clearable
            />
            <el-select
                multiple
                v-model="selectedTranscode"
                style="width: 200px"
                collapse-tags
                placeholder="可播放"
            >
                <el-option
                    v-for="(val, key) in property.transcode"
                    :value="key"
                    :key="key"
                >
                    <span style="float：left">{{ key }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        val
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedFavor"
                style="width: 200px"
                collapse-tags
                placeholder="收藏夹"
            >
                <el-option v-for="(val, key) in property.favor" :value="key" :key="key">
                    <span style="float：left">{{ key }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        val
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedPeople"
                style="width: 200px"
                collapse-tags
                placeholder="人物"
            >
                <el-option v-for="(val, key) in property.people" :value="key" :key="key">
                    <span style="float：left">{{ key }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        val
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedTag"
                style="width: 200px"
                collapse-tags
                placeholder="标签"
            >
                <el-option v-for="(val, key) in property.tag" :value="key" :key="key">
                    <span style="float：left">{{ key }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        val
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedClarity"
                style="width: 200px"
                collapse-tags
                placeholder="清晰度"
            >
                <el-option v-for="(val, key) in property.clarity" :value="key" :key="key">
                    <span style="float：left">{{ key }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        val
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedDirection"
                style="width: 200px"
                collapse-tags
                placeholder="方向"
            >
                <el-option
                    v-for="(val, key) in property.direction"
                    :value="key"
                    :key="key"
                >
                    <span style="float：left">{{ key }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        val
                    }}</span>
                </el-option>
            </el-select>
            <el-button type="primary" @click="handleSearch"> 搜索 </el-button>
        </div>

        <!-- 分页器 -->
        <el-pagination
            layout="prev, pager, next"
            :page-size="pageSize"
            :total="videoNum"
            :current-page="curPage"
            @current-change="handleCurrentPageChange"
        />
        <!--表格上方按钮-->
        <el-button type="primary" @click="handleRefresh"> Refresh </el-button>
        <el-button type="primary" @click="handleTranscode"> Transcode </el-button>
        <el-button type="primary" @click="openBatchDialog"> 批量修改</el-button>
        <el-dialog v-model="showBatchDialog" :width="autoWidth(40)" title="批量修改">
            <el-input v-model="inputFavor" placeholder="收藏夹" />
            <el-button type="primary" @click="setFavor">修改收藏夹</el-button>
            <el-divider />
            <el-input v-model="inputBatchPeople" placeholder="人物（逗号分割）" />
            <el-input v-model="inputBatchTag" placeholder="标签（逗号分割）" />
            <el-button type="primary" @click="setCustom">修改人物、标签</el-button>
        </el-dialog>

        <!-- 表格 -->
        <el-table
            :data="tableData"
            style="width: 100%; height: 100%"
            border="true"
            stripe="true"
            @selection-change="handleTableSelection"
            :row-key="item_name"
            :cell-style="{ padding: 0 + 'px' }"
        >
            <!-- 左侧固定 -->
            <el-table-column :fixed="isLandscapeDevice" type="selection" width="40" />
            <el-table-column
                :fixed="isLandscapeDevice"
                prop="video_info.title"
                label="标题"
                width="300"
            />
            <!-- 中间 -->
            <el-table-column label="封面" width="120">
                <template #default="scope">
                    <el-image :src="scope.row.video_info.cover" fit="contain" />
                </template>
            </el-table-column>
            <el-table-column prop="video_info.owner_name" label="UP主" width="120" />
            <el-table-column prop="favor_name" label="收藏夹" width="80" />
            <el-table-column label="更新时间" width="100">
                <template #default="scope">{{
                    timestamp2date(scope.row.video_info.update_time)
                }}</template>
            </el-table-column>
            <el-table-column label="人物" width="80">
                <template #default="scope">{{
                    scope.row.video_info.custom_info.people.join(",")
                }}</template>
            </el-table-column>
            <el-table-column label="标签" width="80">
                <template #default="scope">{{
                    scope.row.video_info.custom_info.tag.join(",")
                }}</template>
            </el-table-column>
            <el-table-column label="描述">
                <template #default="scope">{{
                    scope.row.video_info.custom_info.description
                }}</template>
            </el-table-column>
            <!-- 右侧固定 -->
            <el-table-column
                :fixed="isLandscapeDevice ? 'right' : false"
                label="操作"
                width="75"
            >
                <template #default="scope">
                    <el-button
                        link
                        type="primary"
                        size="small"
                        @click="handleCustom(scope.row)"
                    >
                        Custom
                    </el-button>
                    <br />
                    <el-button
                        link
                        type="primary"
                        size="small"
                        @click="handleJSON(scope.row)"
                    >
                        JSON
                    </el-button>
                    <br />
                    <el-button
                        link
                        type="primary"
                        size="small"
                        @click="handleLink(scope.row.video_info)"
                        target="_blank"
                    >
                        B站
                    </el-button>
                </template>
            </el-table-column>
            <el-table-column fixed="right" label="播放" width="50">
                <template #default="scope">
                    <el-button
                        type="primary"
                        size="small"
                        circle
                        :disabled="!scope.row.video_info.transcoded"
                        @click="handlePlay(scope.row)"
                    >
                        <!-- 播放icon -->
                        <el-icon>
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                viewBox="0 0 1024 1024"
                                data-v-ea893728=""
                            >
                                <path
                                    fill="currentColor"
                                    d="M384 192v640l384-320.064z"
                                ></path>
                            </svg>
                        </el-icon>
                    </el-button>
                </template>
            </el-table-column>
        </el-table>

        <!--Custom弹窗-->
        <el-dialog v-model="showCustomDialog" :width="autoWidth(40)" title="Custom">
            人物：
            <el-input v-model="inputPeople" placeholder="人物" />
            标签：
            <el-input v-model="inputTag" placeholder="标签" />
            描述：
            <el-input v-model="inputDescription" placeholder="描述" />
            星级：
            <el-input-number v-model="inputStarLevel" :min="0" :max="3" />
            <el-button type="primary" @click="handleCustomSubmit">修改Custom</el-button>
        </el-dialog>
        <!--JSON弹窗-->
        <el-dialog v-model="showJSONDialog" :width="autoWidth(60)" title="JSON">
            <el-input
                v-model="JSONStr"
                :autosize="{ minRows: 10, maxRows: 20 }"
                type="textarea"
            />
        </el-dialog>
    </div>

    <!-- 视频播放 -->
    <div v-show="playVideo" :span="24">
        <el-button type="primary" @click="handleReturnButton"> 返回 </el-button>
        <video
            id="video"
            controls
            loop
            width="100%"
            :height="windowHeight"
            style="object-fit: contain"
            :src="videoUrl()"
        />
        <!-- 播放列表 -->
        <el-row>
            <el-col :span="2">
                <el-button type="primary" size="small" circle @click="true" />
            </el-col>
            <el-col v-for="row in tableData" :key="row" :span="2">
                <el-card :body-style="{ padding: '0px' }">
                    <el-button
                        type="primary"
                        size="small"
                        circle
                        :disabled="!row.video_info.transcoded"
                        @click="handlePlay(row)"
                    >
                        <!-- 播放icon -->
                        <el-icon>
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                viewBox="0 0 1024 1024"
                                data-v-ea893728=""
                            >
                                <path
                                    fill="currentColor"
                                    d="M384 192v640l384-320.064z"
                                ></path>
                            </svg>
                        </el-icon>
                    </el-button>
                    <el-image :src="row.video_info.cover" fit="contain" />
                    <div style="padding: 0px">
                        <el-text size="small">{{ row.video_info.title }}</el-text>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="2">
                <el-button type="primary" size="small" circle @click="true" />
            </el-col>
        </el-row>
    </div>
</template>

<script>
import axios from "axios";

export default {
    /*
        数据
    */
    data() {
        // create生命周期后才实例化
        return {
            // 请求的根路径
            rootUrl: "http://localhost:1024",
            // 分页器
            videoNum: 0, // 视频总量
            curPage: 1, // 当前页
            pageSize: 10,
            // 选中的筛选条件，都是多选
            inputKeywords: "",
            selectedTranscode: [],
            selectedFavor: [],
            selectedPeople: [],
            selectedTag: [],
            selectedClarity: [],
            selectedDirection: [],
            // 表格选中的行
            selectedRow: [],
            // 表格
            tableData: [
                {
                    favor_name: "",
                    item_name: "",
                    page_name: "",
                    video_info: {
                        title: "",
                        page_title: "",
                        type: "",
                        owner_id: 0,
                        owner_name: "",
                        cover: "",
                        update_time: 0,
                        direction: "",
                        size: 0,
                        length: 0,
                        quality: "",
                        height: 0,
                        width: 0,
                        fps: 0.0,
                        bvid: "",
                        avid: 0,
                        custom_info: {
                            people: [],
                            tag: [],
                            description: "",
                            star_level: 0,
                            colletion_time: 0,
                        },
                    },
                },
            ],
            // 属性
            property: {
                favor: {},
                people: {},
                tag: {},
                clarity: {},
                direction: {},
                transcode: {},
            },
            // 播放视频
            playVideo: false,
            playVideoPath: "",
            // 收藏夹弹窗
            showBatchDialog: false,
            inputFavor: "",
            inputBatchPeople: "",
            inputBatchTag: "",
            // Custom弹窗
            showCustomDialog: false,
            customVideoName: "",
            inputPeople: "",
            inputTag: "",
            inputDescription: "",
            inputStarLevel: 0,
            // JSON弹窗
            showJSONDialog: false,
            JSONStr: "",
            //页面高度
            windowHeight: window.innerHeight * 0.9 + "px",
            // 是横屏设备还是竖屏设备，true表示横屏
            isLandscapeDevice: true,
        };
    },

    /*
        方法
    */
    methods: {
        /*
          调接口
        */
        // 获取列表数据
        getData() {
            axios
                .get(
                    this.rootUrl +
                        "/api/video/list?page=" +
                        this.curPage +
                        "&page_size=" +
                        this.pageSize +
                        "&keywords=" +
                        this.inputKeywords +
                        "&transcode=" +
                        this.selectedTranscode +
                        "&favor=" +
                        this.selectedFavor +
                        "&people=" +
                        this.selectedPeople +
                        "&tag=" +
                        this.selectedTag +
                        "&clarity=" +
                        this.selectedClarity +
                        "&direction=" +
                        this.selectedDirection
                )
                .then((response) => {
                    console.log(response);
                    var list = response.data.list.map((val) => {
                        if (
                            val.video_info.page_title != val.video_info.title &&
                            val.video_info.page_title != ""
                        ) {
                            val.video_info.title =
                                val.video_info.title + "--" + val.video_info.page_title;
                        }
                        val.video_info.cover = "cover/" + val.video_info.cover;
                        return val;
                    });
                    this.tableData = list;
                    this.videoNum = response.data.count;
                });
        },
        // 获取Property
        getProperty() {
            axios.get(this.rootUrl + "/api/get-property").then((response) => {
                this.property = response.data;
                console.log(this.property);
            });
        },
        /*
            事件处理Handler
        */
        // 处理页数变更
        handleCurrentPageChange(val) {
            this.curPage = val;
            this.getData();
            console.log(this.curPage);
        },
        // 处理Favor筛选框
        handleSearch() {
            this.getData();
        },
        // 处理Refresh
        handleRefresh() {
            axios.get(this.rootUrl + "/api/refresh").then((response) => {
                console.log(response);
                this.getData();
            });
        },
        // 处理Transcode
        handleTranscode() {
            axios.get(this.rootUrl + "/api/transcode").then((response) => {
                alert(response.data);
            });
        },
        // 处理表格多选
        handleTableSelection(val) {
            this.selectedRow = val;
            console.log(this.selectedRow);
        },
        // 处理播放按钮
        handlePlay(v) {
            this.playVideo = true;
            this.playVideoPath =
                v.favor_name + "/" + v.item_name + "/" + v.page_name + "/";
            setTimeout(() => {
                const video = document.getElementById("video");
                video.play();
            }, 500);
        },
        // 处理Custom按钮
        handleCustom(row) {
            this.showCustomDialog = true;
            this.inputPeople = row.video_info.custom_info.people.join(",");
            this.inputTag = row.video_info.custom_info.tag.join(",");
            this.inputDescription = row.video_info.custom_info.description;
            this.inputStarLevel = row.video_info.custom_info.star_level;
            this.customVideoName = row.item_name + ";" + row.page_name;
        },
        // 处理Custom弹窗中的确认按钮
        handleCustomSubmit() {
            axios
                .put(this.rootUrl + "/api/video/update-custom", {
                    video_name: this.customVideoName,
                    custom_info: {
                        people: this.inputPeople.split(","),
                        tag: this.inputTag.split(","),
                        description: this.inputDescription,
                        star_level: this.inputStarLevel,
                    },
                })
                .then(() => {
                    this.showCustomDialog = false;
                    this.getData();
                    this.getProperty();
                });
        },
        // 处理JSON按钮
        handleJSON(row) {
            this.showJSONDialog = true;
            this.JSONStr = JSON.stringify(row, null, "    ");
        },
        // 处理跳转b站按钮
        handleLink(v) {
            window.open(
                "https://www.bilibili.com/video/" + v.bvid + "?p=" + v.page_order,
                "_blank"
            );
        },
        // 处理返回按钮
        handleReturnButton() {
            this.playVideo = false;
            this.playVideoPath = "";
            const video = document.getElementById("video");
            video.pause();
        },
        // 打开批量修改对话框
        openBatchDialog() {
            this.showBatchDialog = true;
        },
        // 批量修改收藏夹
        setFavor() {
            var videoNameList = [];
            for (var i = 0; i < this.selectedRow.length; i++) {
                var row = this.selectedRow[i];
                videoNameList.push(row.item_name + ";" + row.page_name);
            }
            console.log(videoNameList);
            axios
                .put(this.rootUrl + "/api/video/update-favor", {
                    video_name_list: videoNameList,
                    new_favor_name: this.inputFavor,
                })
                .then(() => {
                    this.showBatchDialog = false;
                    this.getData();
                    this.getProperty();
                });
        },
        // 批量修改人物和标签
        setCustom() {
            var videoNameList = [];
            for (var i = 0; i < this.selectedRow.length; i++) {
                var row = this.selectedRow[i];
                videoNameList.push(row.item_name + ";" + row.page_name);
            }
            console.log(videoNameList);
            axios
                .put(this.rootUrl + "/api/video/batch-add", {
                    video_name_list: videoNameList,
                    people_list: this.inputBatchPeople.split(","),
                    tag_list: this.inputBatchTag.split(","),
                })
                .then(() => {
                    this.showBatchDialog = false;
                    this.getData();
                    this.getProperty();
                });
        },
        /*
          工具Util
        */
        // 视频url转换
        videoUrl() {
            if (this.playVideoPath == "") {
                return "";
            }
            return require("../../../assets/video/" + this.playVideoPath + "out.mp4");
        },
        // 时间戳转日期
        timestamp2date(timestamp) {
            var date = new Date(timestamp);
            var Y = this.dateInt2string(date.getFullYear());
            var M = this.dateInt2string(date.getMonth());
            var D = this.dateInt2string(date.getDate());
            var h = this.dateInt2string(date.getHours());
            var m = this.dateInt2string(date.getMinutes());
            var s = this.dateInt2string(date.getSeconds());
            return Y + "." + M + "." + D + " " + h + ":" + m + ":" + s;
        },
        // 返回的整数转成长度为2的字符串
        dateInt2string(num) {
            if (num < 10) {
                return "0" + num;
            } else return num.toString();
        },
        autoWidth(percent) {
            if (this.isLandscapeDevice) return percent + "%";
            return "100%";
        },
    },

    /*
        挂载时
    */
    mounted() {
        this.getData();
        this.getProperty();
        if (window.innerHeight > window.innerWidth) {
            this.isLandscapeDevice = false;
        }
        window.addEventListener("resize", () => {
            if (window.innerHeight > window.innerWidth) {
                this.isLandscapeDevice = false;
            } else {
                this.isLandscapeDevice = true;
            }
        });
    },
};
</script>
