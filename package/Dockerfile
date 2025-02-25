FROM registry.suse.com/suse/sle15:15.3 as intermediate

#Label the image for cleaning after build process
LABEL stage=intermediate

RUN zypper --non-interactive update \
    && zypper --non-interactive install \
    git

RUN git clone -b v0.6.6 --depth 1 https://github.com/aquasecurity/kube-bench.git

FROM registry.suse.com/suse/sle15:15.3
ARG kube_bench_tag=0.2.3-rancher-1
ARG sonobuoy_version=0.53.2

RUN zypper --non-interactive update \
    && zypper --non-interactive install \
    curl \
    jq \
    vim \
    systemd \
    tar \
    awk
RUN curl -sLf https://storage.googleapis.com/kubernetes-release/release/v1.13.5/bin/linux/amd64/kubectl > /usr/local/bin/kubectl-1.13 \
    && ln -sv /usr/local/bin/kubectl-1.13 /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl*
RUN curl -sLf https://github.com/vmware-tanzu/sonobuoy/releases/download/v${sonobuoy_version}/sonobuoy_${sonobuoy_version}_linux_amd64.tar.gz | tar -xvzf - -C /usr/bin sonobuoy
#RUN curl -sLf https://github.com/aquasecurity/kube-bench/releases/download/v${kube_bench_tag}/kube-bench_${kube_bench_tag}_linux_amd64.tar.gz | tar -xvzf - -C /usr/bin
RUN curl -sLf https://github.com/leodotcloud/kube-bench/releases/download/v${kube_bench_tag}/kube-bench.tar.gz | tar -xvzf - -C /usr/bin

COPY --from=intermediate /kube-bench/cfg /etc/kube-bench/cfg/

COPY package/cfg/ /etc/kube-bench/cfg/
COPY package/run.sh \
    package/run_sonobuoy_plugin.sh \
    bin/kb-summarizer \
    package/helper_scripts/check_files_permissions.sh \
    package/helper_scripts/check_files_owner_in_dir.sh \
    package/helper_scripts/check_encryption_provider_config.sh \
    package/helper_scripts/check_for_network_policies.sh \
    package/helper_scripts/check_for_default_sa.sh \
    package/helper_scripts/check_for_default_ns.sh \
    package/helper_scripts/check_for_k3s_etcd.sh \
    package/helper_scripts/check_for_rke2_network_policies.sh \
    package/helper_scripts/check_for_rke2_cni_net_policy_support.sh \
    package/helper_scripts/check_cafile_permissions.sh \
    package/helper_scripts/check_cafile_ownership.sh \
    /usr/bin/

CMD ["run.sh"]
