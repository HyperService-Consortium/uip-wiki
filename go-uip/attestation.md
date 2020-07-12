# Attestation

Attestation表示了在ISC执行过程中的一个证明(certificate)或者说是一个动作(action)。在ISC执行过程中，一个跨链Transaction的执行过程被分解为多个动作，每个动作由双方交替确认。

在论文中，这些Cert由Attestation表示：

+ $\operatorname{Cert}_{\mathcal{T}}^{\mathsf{i}} = \operatorname{Cert}([\mathcal{T}, \mathsf{init}, \mathsf{sid}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{V}})$

+ $\operatorname{Cert}_{\mathcal{T}}^{\mathsf{id}} = \operatorname{Cert}([\mathcal{T}, \mathsf{inited}, \mathsf{sid}, \tilde{T}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{D}/\mathcal{V}})$

+ $\operatorname{Cert}_{\mathcal{T}}^{\mathsf{o}} = \operatorname{Cert}([\mathcal{T}, \mathsf{open}, \mathsf{sid}, \tilde{T}, \mathsf{ts_{open}}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{V}/\mathcal{D}})$

+ $\operatorname{Cert}_{\mathcal{T}}^{\mathsf{od}} = \operatorname{Cert}([\mathcal{T}, \mathsf{open}, \mathsf{sid}, \tilde{T}, \mathsf{ts_{open}}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{V}/\mathcal{D}}, \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{D}/\mathcal{V}})$

+ $C^{\mathcal{T}}_{\mathsf{closed}} = \operatorname{Cert}([\mathcal{T}, \mathsf{closed}, \mathsf{sid}, \tilde{T}, \mathsf{ts_{closed}}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{V}/\mathcal{D}})$

+ $\operatorname{Cert}_{\mathcal{T}}^{\mathsf{c}} = \operatorname{Cert}([\mathcal{T}, \mathsf{closed}, \mathsf{sid}, \tilde{T}, \mathsf{ts_{closed}}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{D}/\mathcal{V}}, \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{V}/\mathcal{D}})$

由于$\{ \operatorname{Cert}_{\mathcal{T}}^{\mathsf{i}} \}$ 与 $ \operatorname{Cert}([\{\mathcal{T}\}, \mathsf{sid}]; \mathrm{Sig}_{\mathsf{sid}}^{\mathcal{V}})$ 有相似性。在`go-uip/ves`实现中对transaction状态做了略微修改。新的状态集合修改为$\{\mathsf{unknown}, \mathsf{init}, \mathsf{inited}, \mathsf{instantiating} ,\mathsf{open}, \mathsf{opened}, \mathsf{closed}\}$分别对应原论文状态$\{\mathsf{unknown}, \times, \mathsf{init}, \mathsf{inited} ,\mathsf{open}, \mathsf{opened}, \mathsf{closed}\}$。具体定义请查看[TxState](https://github.com/HyperService-Consortium/go-uip/blob/master/const/transaction_state_type/tx_state.go#L7)。

## Attestation

go-uip规定一个有意义的Attestation是以下四元组：

+ `uint64(8 octets)`: `tid`
+ `uint64(8 octets)`: `aid`
+ `octets`: `content`
+ `array of uip.Signature`: `signatures`

它唯一地表示了在一次ISC上下文中的一次action。其中`aid`为状态枚举值；`content`为序列化后的action内容；`signatures`为`uip.Signature`类型的数组用于证明该action中`content`的有效性。当$\mathsf{sid}$确定时，`tid`唯一地确认了一个$\mathcal{T}$，`tid`索引值确定了该$\mathcal{T}$在ISC中发射（开始执行）的顺序。