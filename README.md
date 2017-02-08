## 一、概述
基于Retrofit和RxJava2的网络请求和文件下载工具，其中文件下载支持断点续传和解压回调，自动判断服务端文件是否变化。
## 二、版本
已在多个项目中使用，且已上传jCenter，最新版本1.0.0，直接在gradle中添加即可。
compile 'com.hengda.zwf:HttpUtil:1.0.0'
## 三、使用
具体用法参见demo，demo以检查版本更新和安装包下载为例。

```
private void loadAndInstall(CheckResponse checkResponse) {
        String url = checkResponse.getVersionInfo().getVersionUrl();
        String saveName = url.substring(url.lastIndexOf("/") + 1);
        String savePath = HdAppConfig.getDefaultFileDir();

        RxDownload.getInstance()
                .download(url, saveName, savePath)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .doOnSubscribe(d -> disposable = d)
                .doOnNext(status -> updateProgress(status))
                .doOnError(throwable -> Logger.e("下载失败：" + throwable.getMessage()))
                .doOnComplete(() -> installApk(saveName, savePath))
                .subscribe();
    }

    private void installApk(String saveName, String savePath) {
        String apkPath = TextUtils.concat(savePath, saveName).toString();
        AppUtil.installApk(CheckUpdateActivity.this, apkPath);
    }

    private void updateProgress(DownloadStatus status) {
        txtProgress.setText(String.format("正在下载(%s/%s)",
                DataManager.getFormatSize(status.getDownloadSize()),
                DataManager.getFormatSize(status.getTotalSize())));
    }
```