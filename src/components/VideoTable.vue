<template>
    <!-- 列表 -->
    <div v-show="!playVideo">
        <!-- 对列表进行条件筛选 -->
        <div>
            <!-- 常显条件 -->
            <el-input
                v-model="inputKeywords"
                style="width: 150px"
                placeholder="关键词"
                clearable
                @change="handleChangeFilter"
            />
            <el-select
                multiple
                v-model="selectedFavorList"
                style="width: 150px"
                collapse-tags
                placeholder="收藏夹"
                @change="handleChangeFilter"
            >
                <el-option
                    v-for="(item, key) in property.favor"
                    :value="item.name"
                    :key="key"
                >
                    <span style="float：left">{{ item.name }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        item.count
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                v-model="selectedDirection"
                style="width: 150px"
                collapse-tags
                placeholder="方向(单选)"
                clearable
                @change="handleChangeFilter"
            >
                <el-option
                    v-for="(item, key) in property.direction"
                    :value="item.name"
                    :key="key"
                >
                    <span style="float：left">{{ item.name }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        item.count
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedPeopleList"
                style="width: 150px"
                collapse-tags
                placeholder="人物"
                @change="handleChangeFilter"
            >
                <el-option
                    v-for="(item, key) in property.people"
                    :value="item.name"
                    :key="key"
                >
                    <span style="float：left">{{ item.name }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        item.count
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                multiple
                v-model="selectedTagList"
                style="width: 150px"
                collapse-tags
                placeholder="标签"
                @change="handleChangeFilter"
            >
                <el-option
                    v-for="(item, key) in property.tag"
                    :value="item.name"
                    :key="key"
                >
                    <span style="float：left">{{ item.name }}</span>
                    <span style="float: right; color: var(--el-text-color-secondary)">{{
                        item.count
                    }}</span>
                </el-option>
            </el-select>
            <el-select
                v-model="selectedSort"
                style="width: 150px"
                collapse-tags
                placeholder="排序"
                @change="handleChangeFilter"
            >
                <el-option
                    v-for="(val, key) in sortMappings"
                    :value="val.value"
                    :label="val.label"
                    :key="key"
                />
            </el-select>
            <br />

            <!-- 折叠条件 -->
            <el-collapse v-model="activeFilterList">
                <el-collapse-item title="其他条件" name="1">
                    <el-select
                        multiple
                        v-model="selectedExcludeFavorList"
                        style="width: 150px"
                        collapse-tags
                        placeholder="排除收藏夹"
                        @change="handleChangeFilter"
                    >
                        <el-option
                            v-for="(item, key) in property.favor"
                            :value="item.name"
                            :key="key"
                        >
                            <span style="float：left">{{ item.name }}</span>
                            <span
                                style="
                                    float: right;
                                    color: var(--el-text-color-secondary);
                                "
                                >{{ item.count }}</span
                            >
                        </el-option>
                    </el-select>
                    <el-select
                        multiple
                        v-model="selectedExcludePeopleList"
                        style="width: 150px"
                        collapse-tags
                        placeholder="排除人物"
                        @change="handleChangeFilter"
                    >
                        <el-option
                            v-for="(item, key) in property.people"
                            :value="item.name"
                            :key="key"
                        >
                            <span style="float：left">{{ item.name }}</span>
                            <span
                                style="
                                    float: right;
                                    color: var(--el-text-color-secondary);
                                "
                                >{{ item.count }}</span
                            >
                        </el-option>
                    </el-select>
                    <el-select
                        multiple
                        v-model="selectedExcludeTagList"
                        style="width: 150px"
                        collapse-tags
                        placeholder="排除标签"
                        @change="handleChangeFilter"
                    >
                        <el-option
                            v-for="(item, key) in property.tag"
                            :value="item.name"
                            :key="key"
                        >
                            <span style="float：left">{{ item.name }}</span>
                            <span
                                style="
                                    float: right;
                                    color: var(--el-text-color-secondary);
                                "
                                >{{ item.count }}</span
                            >
                        </el-option>
                    </el-select>
                    <el-select
                        multiple
                        v-model="selectedClarityList"
                        style="width: 150px"
                        collapse-tags
                        placeholder="清晰度"
                        @change="handleChangeFilter"
                    >
                        <el-option
                            v-for="(item, key) in property.clarity"
                            :value="item.name"
                            :key="key"
                        >
                            <span style="float：left">{{ item.name }}</span>
                            <span
                                style="
                                    float: right;
                                    color: var(--el-text-color-secondary);
                                "
                                >{{ item.count }}</span
                            >
                        </el-option>
                    </el-select>
                    <el-select
                        v-model="selectedVcodec"
                        style="width: 150px"
                        collapse-tags
                        placeholder="视频编码(单选)"
                        clearable
                        @change="handleChangeFilter"
                    >
                        <el-option
                            v-for="(item, key) in property.vcodec"
                            :value="item.name"
                            :key="key"
                        >
                            <span style="float：left">{{ item.name }}</span>
                            <span
                                style="
                                    float: right;
                                    color: var(--el-text-color-secondary);
                                "
                                >{{ item.count }}</span
                            >
                        </el-option>
                    </el-select>
                    <el-input-number v-model="min_duration" :step="60" :min="0"/>
                    <el-input-number v-model="max_duration" :step="60" :min="0"/>
                </el-collapse-item>
            </el-collapse>
            <el-button type="primary" @click="handleReset"> 清空 </el-button>
            <el-button type="primary" @click="handleChangeFilter"> 搜索 </el-button>
        </div>

        <!-- 表格上方分页器 -->
        <!-- layout还可以加sizes, jumper -->
        <el-pagination
            layout="prev, pager, next, total"
            :page-size="pageSize"
            :total="videoNum"
            :current-page="curPage"
            @current-change="handleCurrentPageChange"
        />

        <!--表格上方按钮-->
        <el-button type="primary" @click="handleRefresh"> Refresh </el-button>
        <el-button type="primary" @click="handleAutoMap"> AutoMap </el-button>
        <el-button type="primary" @click="openBatchDialog"> 批量修改</el-button>
        <el-dialog v-model="showBatchDialog" :width="autoWidth(40)" title="批量修改">
            <el-input
                v-model="inputFavor"
                list="favorDataList"
                clearable
                placeholder="收藏夹"
            />
            <el-button type="primary" @click="setFavor">修改收藏夹</el-button>
            <el-divider />
            <el-input
                v-model="inputBatchPeople"
                list="peopleDataList"
                clearable
                placeholder="人物（逗号分割）"
            />
            <el-input
                v-model="inputBatchTag"
                list="tagDataList"
                clearable
                placeholder="标签（逗号分割）"
            />
            <el-button type="primary" @click="handleBatchSubmit">修改人物、标签</el-button>
        </el-dialog>
        <datalist id="favorDataList">
            <option v-for="(item, key) in property.favor" :key="key" :value="item.name" />
        </datalist>
        <datalist id="peopleDataList">
            <option v-for="(item, key) in property.people" :key="key" :value="item.name" />
        </datalist>
        <datalist id="tagDataList">
            <option v-for="(item, key) in property.tag" :key="key" :value="item.name" />
        </datalist>

        <!-- 表格 -->
        <el-table
            ref="table"
            :data="tableData"
            style="width: 100%; height: 100%"
            border="true"
            stripe="true"
            @selection-change="handleTableSelection"
            :row-key="(row) => row.page_name+';'+row.page_name"
        >
            <!-- 左侧固定 -->
            <el-table-column type="selection" width="25" :reserve-selection="true" />
            <el-table-column
                :fixed="isLandscapeDevice"
                prop="video_info.total_title"
                label="标题"
                width="200"
            />
            <!-- 中间 -->
            <el-table-column label="封面" width="120">
                <template #default="scope">
                    <div class="block">
                        <el-image
                            :src="coverUrl(scope.row.video_info.cover)"
                            :preview-src-list="[coverUrl(scope.row.video_info.cover)]"
                            preview-teleported="true"
                            style="height: 72px"
                        />
                    </div>
                </template>
            </el-table-column>
            <el-table-column label="人物" width="65">
                <template #default="scope">{{
                    scope.row.video_info.custom_info.people.join(",")
                }}</template>
            </el-table-column>
            <el-table-column prop="favor_name" label="收藏夹" width="75" />
            <el-table-column prop="video_info.owner_name" label="UP主" width="100" />
            <el-table-column label="发布时间" width="80">
                <template #default="scope">{{
                    timestamp2date(scope.row.video_info.custom_info.publish_time)
                }}</template>
            </el-table-column>
            <el-table-column label="星级" width="30">
                <template #default="scope">
                    <img
                        v-for="i in scope.row.video_info.custom_info.star_level"
                        src="star.png"
                        width="15px"
                        height="15px"
                        :key="i"
                /></template>
            </el-table-column>
            <el-table-column label="标签" width="80">
                <template #default="scope">{{
                    scope.row.video_info.custom_info.tag.join(",")
                }}</template>
            </el-table-column>
            <el-table-column label="时长" width="40">
                <template #default="scope">{{
                    this.duration2string(scope.row.video_info.duration)
                }}</template>
            </el-table-column>
            <el-table-column prop="video_info.custom_info.description" label="描述" />
            <!-- 右侧固定 -->
            <el-table-column
                :fixed="isLandscapeDevice ? 'right' : false"
                label="操作"
                width="50"
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
            <el-table-column fixed="right" label="播放" width="30">
                <template #default="scope">
                    <el-button
                        type="primary"
                        size="small"
                        circle
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

        <!-- 表格下方分页器 -->
        <el-pagination
            layout="prev, pager, next, total"
            :page-size="pageSize"
            :total="videoNum"
            :current-page="curPage"
            @current-change="handleCurrentPageChange"
        />

        <!--Custom弹窗-->
        <el-dialog v-model="showCustomDialog" :width="autoWidth(40)" title="Custom">
            <el-row>
                <el-col :span="4">人物：</el-col>
                <el-col :span="20">
                    <el-input
                        v-model="inputPeople"
                        list="peopleDataList"
                        clearable
                        placeholder="人物"
                    />
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="4">标签：</el-col>
                <el-col :span="20">
                    <el-input
                        v-model="inputTag"
                        list="tagDataList"
                        clearable
                        placeholder="标签"
                    />
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="4">描述：</el-col>
                <el-col :span="20">
                    <el-input v-model="inputDescription" clearable placeholder="描述" />
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="4">星级：</el-col>
                <el-col :span="20">
                    <el-input-number v-model="inputStarLevel" :min="0" :max="3" />
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="4">need_h264：</el-col>
                <el-col :span="20">
                    <el-select v-model="need_h264" placeholder="need_h264">
                        <el-option :value="false" label="false" />
                        <el-option :value="true" label="true" />
                    </el-select>
                </el-col>
            </el-row>
            <el-button type="primary" @click="handleCustomSubmit">修改Custom</el-button>
        </el-dialog>
        <!--JSON弹窗-->
        <el-dialog v-model="showJSONDialog" :width="autoWidth(60)" :title="JsonType">
            <el-input
                v-model="JSONStr"
                :autosize="{ minRows: 10, maxRows: 20 }"
                type="textarea"
            />
            <el-button
                type="primary"
                @click="handleAutoMapSubmit"
                v-show="JsonType == 'automap'"
                >修改automap</el-button
            >
        </el-dialog>
    </div>

    <!-- 视频播放 -->
    <div v-show="playVideo" :span="24">
        <video id="video" autoplay controls loop :src="videoUrl()" />
        <div id="underVideo">
            <el-button type="primary" @click="handleNextButton(-1)"> 上一个 </el-button>
            <el-button type="primary" @click="handleReturnButton"> 返回 </el-button>
            <el-button type="primary" @click="handleNextButton(1)"> 下一个 </el-button>
            <!-- 分页器 -->
            <el-pagination
                layout="prev, pager, next, total"
                :page-size="pageSize"
                :total="videoNum"
                :current-page="curPage"
                @current-change="handleCurrentPageChange"
            />
            <!-- 播放列表 -->
            <el-row>
                <el-col v-for="(row, key) in tableData" :key="key" :span="cardSpan">
                    <el-card :body-style="{ padding: '0px' }" style="height: 120px">
                        <el-button
                            type="primary"
                            size="small"
                            circle
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
                        <div style="height: 60px">
                            <el-image
                                :src="coverUrl(row.video_info.cover)"
                                style="height: 60px"
                                :fit="contain"
                            />
                        </div>
                        <div style="padding: 0px">
                            <el-text size="small">{{
                                row.video_info.total_title
                            }}</el-text>
                        </div>
                    </el-card>
                </el-col>
            </el-row>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import { ElMessage } from "element-plus";

export default {
    /*
        数据
    */
    data() {
        // create生命周期后才实例化
        return {
            // 请求的根路径
            rootUrl: process.env.VUE_APP_ROOT_URL,
            // 分页器
            videoNum: 0, // 视频总量
            curPage: 1, // 当前页
            pageSize: 48,
            // 以下列表的变量表示多选
            selectedSort: -1,
            inputKeywords: "",
            selectedFavorList: [],
            selectedDirection: "",
            selectedPeopleList: [],
            selectedTagList: [],
            selectedClarityList: [],
            selectedVcodec: "",
            selectedExcludeFavorList: [],
            selectedExcludePeopleList: [],
            selectedExcludeTagList: [],
            min_duration: 0,
            max_duration: 0,
            // 表格选中的行
            selectedRowList: [],
            // 表格
            tableData: [],
            // 属性
            property: {},
            // 排序映射
            sortMappings: [
                { label: "随机排序", value: 0},
                { label: "发布时间倒序", value: -1 },
                { label: "发布时间顺序", value: 1 },
                { label: "下载时间倒序", value: -2},
                { label: "星级倒序", value: -3 },
                { label: "标题顺序", value: 4 },
                { label: "UP主顺序", value: 5 }
            ],
            // 播放视频
            playVideo: false,
            playVideoKey: "",
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
            need_h264: false,
            // JSON弹窗
            JsonType: "",
            showJSONDialog: false,
            JSONStr: "",
            //页面高度
            windowHeight: window.innerHeight + "px",
            windowWidth: window.innerWidth + "px",
            windowHeight_96: window.innerHeight*0.96 + "px",
            // 是横屏设备还是竖屏设备，true表示横屏
            isLandscapeDevice: true,
            // 播放列表card的span
            cardSpan: 2,
            // 折叠面板
            activeFilterList: [],
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
        getData(newRandomSort) {
            axios
                .get(
                    this.rootUrl +
                        "/api/video/list?page=" +
                        this.curPage +
                        "&page_size=" +
                        this.pageSize +
                        "&sort=" +
                        this.selectedSort +
                        "&new_random_sort="+
                        newRandomSort+
                        "&keywords=" +
                        this.inputKeywords +
                        "&favor=" +
                        this.selectedFavorList +
                        "&direction=" +
                        this.selectedDirection +
                        "&people=" +
                        this.selectedPeopleList +
                        "&tag=" +
                        this.selectedTagList +
                        "&exclude_favor=" +
                        this.selectedExcludeFavorList +
                        "&exclude_people=" +
                        this.selectedExcludePeopleList +
                        "&exclude_tag=" +
                        this.selectedExcludeTagList +
                        "&clarity=" +
                        this.selectedClarityList +
                        "&vcodec=" +
                        this.selectedVcodec+
                        "&min_duration="+
                        this.min_duration+
                        "&max_duration="+
                        this.max_duration
                )
                .then((response) => {
                    console.log(response);
                    var list = response.data.list.map((val) => {
                        if (
                            val.video_info.page_title != val.video_info.title &&
                            val.video_info.page_title != ""
                        ) {
                            val.video_info.total_title =
                                val.video_info.title + "--" + val.video_info.page_title;
                        } else val.video_info.total_title = val.video_info.title;
                        return val;
                    });
                    console.log("!!!!!")
                    console.log(list)
                    this.tableData = list;
                    console.log(this.tableData)
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
            this.getData(false);
            console.log(this.curPage);
        },
        // 处理重置
        handleReset() {
            this.selectedSort = -1;
            this.inputKeywords = "";
            this.selectedFavorList = [];
            this.selectedDirection = "";
            this.selectedPeopleList = [];
            this.selectedTagList = [];
            this.selectedExcludeFavorList = [];
            this.selectedExcludePeopleList = [];
            this.selectedExcludeTagList = [];
            this.selectedClarityList = [];
            this.selectedVcodec = "";
            this.min_duration = 0;
            this.max_duration = 0;
            this.$refs.table.clearSelection();
            this.selectedRowList = [],
            this.getData(true);
        },
        // 处理修改筛选条件
        handleChangeFilter() {
            this.curPage = 1;
            this.getData(true);
        },
        // 处理Refresh
        handleRefresh() {
            ElMessage({
                message: "已开始refresh，完成后列表将自动刷新",
                type: "success",
            });
            axios.get(this.rootUrl + "/api/refresh").then((response) => {
                console.log(response);
                this.curPage = 1;
                ElMessage({
                    message: "refresh完毕",
                    type: "success",
                });
                this.getData(true);
                this.getProperty();
            });
        },
        // // 处理Transcode
        // handleTranscode() {
        //     axios.get(this.rootUrl + "/api/transcode").then((response) => {
        //         alert(response.data);
        //     });
        // },
        // 处理AutoMap
        handleAutoMap() {
            this.JsonType = "automap";
            this.showJSONDialog = true;
            axios.get(this.rootUrl + "/api/get-automap").then((response) => {
                this.JSONStr = response.data;
            });
        },
        // 处理修改AutoMap按钮
        handleAutoMapSubmit() {
            axios
                .put(this.rootUrl + "/api/update-automap", {
                    json_str: this.JSONStr,
                })
                .then(() => {
                    this.showJSONDialog = false;
                });
        },
        // 处理表格多选
        handleTableSelection(val) {
            this.selectedRowList = val;
            console.log(this.selectedRowList);
        },
        // 处理播放按钮
        handlePlay(v) {
            this.playVideo = true;
            this.playVideoKey = v.item_name + ";" + v.page_name;
            const video = document.getElementById("video");
            const underVideo = document.getElementById("underVideo");
            if (!this.isLandscapeDevice && v.video_info.direction == "horizontal") {
                video.style = `width:${this.windowHeight_96}; height:${this.windowWidth}; transform:rotate(90deg); transform-origin:0 0; position:absolute; left:${this.windowWidth}`;
            } else {
                video.style = `width:${this.windowWidth}; height:${this.windowHeight_96}`;
            }
            underVideo.style = `position:absolute; top:${this.windowHeight_96}`;
            setTimeout(() => {
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
            this.need_h264 = row.video_info.custom_info.need_h264;
            this.customVideoName = row.item_name + ";" + row.page_name;
        },
        // 处理Custom弹窗中的确认按钮
        handleCustomSubmit() {
            var people_list = [];
            if (this.inputPeople != "") {
                people_list = this.inputPeople.split(",");
            }
            var tag_list = [];
            if (this.inputTag != "") {
                tag_list = this.inputTag.split(",");
            }
            axios
                .put(this.rootUrl + "/api/video/update-custom", {
                    video_name: this.customVideoName,
                    custom_info: {
                        people: people_list,
                        tag: tag_list,
                        description: this.inputDescription,
                        star_level: this.inputStarLevel,
                        need_h264: this.need_h264,
                    },
                })
                .then(() => {
                    this.showCustomDialog = false;
                    this.getData(false);
                    this.getProperty();
                });
        },
        // 处理JSON按钮
        handleJSON(row) {
            this.JsonType = "data";
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
            this.playVideoKey = "";
            const video = document.getElementById("video");
            video.pause();
        },
        // 处理播放上一个、下一个按钮
        handleNextButton(val) {
            var index = 0;
            for (var i = 0; i < this.tableData.length; i++) {
                var row = this.tableData[i];
                var key = row.item_name + ";" + row.page_name;
                if (this.playVideoKey == key) {
                    index = i;
                    break;
                }
            }
            if (index + val >= 0 && index + val < this.pageSize) {
                index = index + val;
            }
            this.handlePlay(this.tableData[index]);
        },
        // 打开批量修改对话框
        openBatchDialog() {
            this.showBatchDialog = true;
        },
        // 批量修改收藏夹
        setFavor() {
            var videoNameList = [];
            for (var i = 0; i < this.selectedRowList.length; i++) {
                var row = this.selectedRowList[i];
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
                    this.$refs.table.clearSelection();
                    this.selectedRowList = [];
                    this.getData(false);
                    this.getProperty();
                });
        },
        // 批量修改人物和标签
        handleBatchSubmit() {
            var videoNameList = [];
            for (var i = 0; i < this.selectedRowList.length; i++) {
                var row = this.selectedRowList[i];
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
                    this.$refs.table.clearSelection();
                    this.selectedRowList = [];
                    this.getData(false);
                    this.getProperty();
                });
        },
        /*
          工具Util
        */
        // 封面url转换
        coverUrl(cover) {
            if (cover == "") {
                return "";
            }
            return this.rootUrl + "/cover/" + cover;
        },
        // 视频url转换
        videoUrl() {
            if (this.playVideoKey == "") {
                return "";
            }
            return this.rootUrl + "/video/" + this.playVideoKey + ".mp4";
        },
        // 时长（毫秒）转为字符串
        duration2string(duration){
            var m = Math.floor(duration/60);
            var s = duration-60*m;
            var res = m + ":";
            if (s<10){
                res+="0";
            }
            res += s;
            return res;
        },
        // 时间戳转日期
        timestamp2date(timestamp) {
            if (timestamp<=0) return "";
            var date = new Date(timestamp*1000);
            var Y = this.dateInt2string(date.getFullYear());
            var M = this.dateInt2string(date.getMonth() + 1);
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
        console.log("rootUrl", this.rootUrl);
        this.getData(true);
        this.getProperty();
        if (window.innerHeight > window.innerWidth) {
            this.isLandscapeDevice = false;
            this.cardSpan = 4;
        }
        window.addEventListener("resize", () => {
            this.windowHeight = window.innerHeight + "px";
            this.windowWidth = window.innerWidth + "px";
            this.windowHeight_96 = window.innerHeight*0.96 + "px";
            if (window.innerHeight > window.innerWidth) {
                this.isLandscapeDevice = false;
                this.cardSpan = 4;
            } else {
                this.isLandscapeDevice = true;
                this.cardSpan = 2;
            }
        });
    },
};
</script>
<style scoped>
:deep(.block) {
    height: 72px;
    text-align: center;
}
:deep(.el-table .cell) {
    padding: 0 2px;
}
:deep(.el-table .el-table__cell) {
    padding: 0 0;
}
:deep(.el-collapse) {
    --el-collapse-header-height: 20px;
}
:deep(.el-collapse-item__content) {
    padding-bottom: 6px;
}
:deep(.el-pagination) {
    --el-pagination-button-width: 24px;
}
</style>
