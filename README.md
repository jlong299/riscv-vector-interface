# FVI (Flexible RISC-V Vector Interface)
FVI is a high-performance interface designed to connect a RISC-V vector core with a scalar core. Primarily, it is aimed at schemes involving **out-of-order** vector and scalar cores. However, it is also compatible with in-order architectures.

> Version: 0.1.0 (draft)

# List of all interface signals
**Instruction dispatch**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| instr | S -> V | NDispatch x 32 | Vector instructions dispatched from scalar to vector |
| vcsr | S -> V | NDispatch x WVCSR | Some CSR values |
| rob_idx | S -> V | NDispatch x WRobIdx | ROB index of each instruction |
| rob_idx_vsetvl | S -> V | NDispatch x WRobIdx | ROB index of the vsetvl instruction that configures this instruction |
| valid | S -> V | NDispatch | Valid of each instruction |
| ready | V -> S | 1 | Vector core is ready to receive instructions |

**Delayed data**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| vtype | S -> V | 9 | vtype CSR |
| rob_idx_vtype | S -> V | WRobIdx | ROB index of vtype (vsetvl) |
| valid_vtype | S -> V | 1 | Valid of vtype |
| vl | S -> V | WVL | vl CSR |
| rob_idx_vl | S -> V | WRobIdx | ROB index of vl (vsetvl) |
| valid_vl | S -> V | 1 | Valid of vl |
| rs | S -> V | NRs x 64 | Scalar operand that vector instruction needs |
| rob_idx_rs | S -> V | NRs x WRobIdx | ROB index of each scalar operand |
| valid_rs | S -> V | NRs | Valid of each scalar operand |

**Complete**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| rob_idx | V -> S | NComplete x WRobIdx | ROB index of each completed instruction |
| info | V -> S | NComplete x 7 | Information: illegal/fflag/vxsat |
| valid | V -> S | NComplete | Valid of complete instructions |
| rd | V -> S | 64 | Scalar destination register |
| valid_rd | V -> S | 1 | Valid of rd |

**Commit**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| vec_rob_idx_can_commit | S -> V | WRobIdx | Youngest ROB index of vector instruction that can commit |
| valid_can_commit | S -> V | 1 | Valid of vec_rob_idx_can_commit |
| vec_rob_idx_committed | V -> S | WRobIdx | Youngest ROB index of vector instruction that has committed |
| valid_committed | V -> S | 1 | Valid of vec_rob_idx_committed |

**Redirection**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| vec_rob_idx_redirect_req | S -> V | WRobIdx | Redirection target ROB index of vector instruction |
| valid_redirect_req | S -> V | 1 | Valid of vec_rob_idx_redirect_req |
| vec_rob_idx_redirect_resp | V -> S | WRobIdx | Inform scalar core the completion of vector redirection |
| valid_redirect_resp | V -> S | 1 | Valid of vec_rob_idx_redirect_resp |

**Load**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| sop_ld_req | V -> LSU | 1 | Start of a vector load request packet |
| eop_ld_req | V -> LSU | 1 | End of a vector load request packet |
| valid_ld_req | V -> LSU | 1 | Valid of the payload of the load request packet |
| ready_ld_req | LSU -> V | 1 | LSU is ready to receive the next payload of the request packet |
| payload_ld_req | V -> LSU | WLdPayload | Payload of the request, such as vl, mask, and indexed addresses |
| sop_ld_resp | LSU -> V | 1 | Start of a vector load response packet |
| eop_ld_resp | LSU -> V | 1 | End of a vector load response packet |
| valid_ld_resp | LSU -> V | 1 | Valid of the payload of the load response packet |
| rob_idx_ld_resp | LSU -> V | WRobIdx | ROB index of the response packet |
| data_ld_resp | LSU -> V | VLEN | Load data that corresponds to a single vector register |
| mask_ld_resp | LSU -> V | vlenb | Mask of data_ld_resp |
| reg_idx_ld_resp | LSU -> V | 3 | Index of load register inside the register group|

**Store**
| Name | Direction | Width (bits) | Description |
| --- | --- | --- | --- |
| sop_st_req | V -> LSU | 1 | Start of a vector store request packet |
| eop_st_req | V -> LSU | 1 | End of a vector store request packet |
| valid_st_req | V -> LSU | 1 | Valid of the payload of the store request packet |
| ready_st_req | LSU -> V | 1 | LSU is ready to receive the next payload of the request packet |
| payload_st_req | V -> LSU | WStPayload | Payload of the request, such as vl, mask, and indexed addresses |
| data_st_req | V -> LSU | VLEN | Store data that corresponds to a single vector register |
| reg_idx_st_req | V -> LSU | 3 | Index of store register inside the register group|
| can_commit_st_resp | LSU -> V | 1 | Store instruction can be committed (ordered) |



# Todo
Variable parameters:
NDispatch
rob_idx/rob_idx_vsetvl can be NDelayRobIdx later. NDelayRobIdx should be constant.

sop_ld_req: 1) allocate phy reg 2) wait for old_vd if needed