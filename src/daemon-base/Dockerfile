# CEPH DAEMON BASE IMAGE

FROM __ENV_[BASE_IMAGE]__

ENV I_AM_IN_A_CONTAINER 1

__DOCKERFILE_TRACEABILITY_LABELS__

ENV CEPH_VERSION __ENV_[CEPH_VERSION]__
ENV CEPH_POINT_RELEASE "__ENV_[CEPH_POINT_RELEASE]__"
ENV CEPH_DEVEL __ENV_[CEPH_DEVEL]__
ENV CEPH_REF __ENV_[CEPH_REF]__
ENV OSD_FLAVOR __ENV_[OSD_FLAVOR]__

#======================================================
# Install ceph and dependencies, and clean up
#======================================================

__DOCKERFILE_PREINSTALL__

# Escape char after immediately after RUN allows comment in first line
RUN \
    # Install all components for the image, whether from packages or web downloads.
    # 安装映像的所有组件，无论是从软件包还是从web下载。
    # Typical workflow: add new repos; refresh repos; install packages; package-manager clean;
    #   download and install packages from web, cleaning any files as you go.
    # 典型的工作流程：添加新的仓库；刷新仓库；安装软件包；包装管理器清理；从网上下载并安装软件包，清理所有文件。
    # Installs should support install of ganesha for luminous
    # 安装程序应支持为luminous安装ganesha
    __DOCKERFILE_INSTALL__ && \
    # Clean container, starting with record of current size (strip / from end)
    INITIAL_SIZE="$(bash -c 'sz="$(du -sm --exclude=/proc /)" ; echo "${sz%*/}"')" && \
    #
    #
    # Perform any final cleanup actions like package manager cleaning, etc.
    # 执行清理
    __DOCKERFILE_POSTINSTALL_CLEANUP__ && \
    # Tweak some configuration files on the container system
    # 调整容器系统上的一些配置文件
    __DOCKERFILE_POSTINSTALL_TWEAKS__ && \
    # Clean common files like /tmp, /var/lib, etc.
    # 清理常见文件，如：/tmp, /var/lib 等
    __DOCKERFILE_CLEAN_COMMON__ && \
    #
    #
    # Report size savings (strip / from end)
    FINAL_SIZE="$(bash -c 'sz="$(du -sm --exclude=/proc /)" ; echo "${sz%*/}"')" && \
    REMOVED_SIZE=$((INITIAL_SIZE - FINAL_SIZE)) && \
    echo "Cleaning process removed ${REMOVED_SIZE}MB" && \
    echo "Dropped container size from ${INITIAL_SIZE}MB to ${FINAL_SIZE}MB" && \
    #
    # Verify that the packages installed haven't been accidentally cleaned
    # 验证安装的程序包是否未被意外清理
    __DOCKERFILE_VERIFY_PACKAGES__ && echo 'Packages verified successfully'
