```shell
============================= test session starts ==============================
platform darwin -- Python 3.12.12, pytest-9.0.0, pluggy-1.6.0
rootdir: /Users/cloud/tangcong/tmp/parallax
configfile: pyproject.toml
plugins: mock-3.15.1, anyio-4.11.0, cov-7.0.0
collected 67 items

tests/scheduler_tests/test_layer_allocation.py .FFFFF....FFFF....FF      [ 29%]
tests/scheduler_tests/test_request_routing.py ...........                [ 46%]
tests/scheduler_tests/test_scheduler.py ....                             [ 52%]
tests/test_batch_scheduler.py ....                                       [ 58%]
tests/test_executor.py .                                                 [ 59%]
tests/test_message_util.py .........                                     [ 73%]
tests/test_model.py ..                                                   [ 76%]
tests/test_sampler.py .                                                  [ 77%]
tests/test_server_args.py ......                                         [ 86%]
tests/test_shard_loader.py .........                                     [100%]

=================================== FAILURES ===================================
_________ test_water_filling_rebalance[21-gpu_types0-expected_layers0] _________

num_layers = 21, gpu_types = ['a100-80g', 'rtx5090', 'rtx4090']
expected_layers = [13, 5, 3]

    @pytest.mark.parametrize(
        "num_layers,gpu_types,expected_layers",
        [
            (21, ["a100-80g", "rtx5090", "rtx4090"], [13, 5, 3]),
            (15, ["a100-80g", "rtx5090"], [11, 4]),
            # (20 * 312 : 20 * 165 : 20 * 82.6) / 559.6 = 11.1 : 5.8 : 2.9 -> 12 : 5 : 3
            (20, ["a100-80g", "rtx5090", "rtx4090"], [12, 5, 3]),
            (25, ["a100-80g", "rtx5090", "rtx4090", "rtx4090"], [13, 5, 4, 3]),
            (29, ["rtx4090", "a100-80g", "rtx5090", "rtx5090", "rtx4090"], [3, 13, 5, 5, 3]),
            (8, ["rtx5090", "rtx5090"], [4, 4]),
            (7, ["a100-40g", "rtx5090"], [5, 2]),
        ],
    )
    def test_water_filling_rebalance(num_layers: int, gpu_types: list[str], expected_layers: list[int]):
        """Test water-filling rebalancer with various GPU configurations."""
        model = build_model_info(num_layers)
    
        nodes = [_build_node(g, model, id_suffix=f"-{i}") for i, g in enumerate(gpu_types)]
    
        allocator = GreedyLayerAllocator(model, nodes)
        allocator.adjust_pipeline_layers(nodes, assume_sorted=False)
    
        actual_layers = []
        for node in nodes:
            assert node.start_layer is not None and node.end_layer is not None
            actual_layers.append(node.end_layer - node.start_layer)
    
        assert sum(actual_layers) == num_layers
        # Order-insensitive: only the multiset of stage sizes must match
>       assert Counter(actual_layers) == Counter(expected_layers)
E       assert Counter({12: 1, 6: 1, 3: 1}) == Counter({13: 1, 5: 1, 3: 1})
E         
E         Omitting 1 identical items, use -vv to show
E         Left contains 2 more items:
E         {6: 1, 12: 1}
E         Right contains 2 more items:
E         {5: 1, 13: 1}
E         Use -v to get more diff

tests/scheduler_tests/test_layer_allocation.py:84: AssertionError
_________ test_water_filling_rebalance[15-gpu_types1-expected_layers1] _________

num_layers = 15, gpu_types = ['a100-80g', 'rtx5090'], expected_layers = [11, 4]

    @pytest.mark.parametrize(
        "num_layers,gpu_types,expected_layers",
        [
            (21, ["a100-80g", "rtx5090", "rtx4090"], [13, 5, 3]),
            (15, ["a100-80g", "rtx5090"], [11, 4]),
            # (20 * 312 : 20 * 165 : 20 * 82.6) / 559.6 = 11.1 : 5.8 : 2.9 -> 12 : 5 : 3
            (20, ["a100-80g", "rtx5090", "rtx4090"], [12, 5, 3]),
            (25, ["a100-80g", "rtx5090", "rtx4090", "rtx4090"], [13, 5, 4, 3]),
            (29, ["rtx4090", "a100-80g", "rtx5090", "rtx5090", "rtx4090"], [3, 13, 5, 5, 3]),
            (8, ["rtx5090", "rtx5090"], [4, 4]),
            (7, ["a100-40g", "rtx5090"], [5, 2]),
        ],
    )
    def test_water_filling_rebalance(num_layers: int, gpu_types: list[str], expected_layers: list[int]):
        """Test water-filling rebalancer with various GPU configurations."""
        model = build_model_info(num_layers)
    
        nodes = [_build_node(g, model, id_suffix=f"-{i}") for i, g in enumerate(gpu_types)]
    
        allocator = GreedyLayerAllocator(model, nodes)
        allocator.adjust_pipeline_layers(nodes, assume_sorted=False)
    
        actual_layers = []
        for node in nodes:
            assert node.start_layer is not None and node.end_layer is not None
            actual_layers.append(node.end_layer - node.start_layer)
    
        assert sum(actual_layers) == num_layers
        # Order-insensitive: only the multiset of stage sizes must match
>       assert Counter(actual_layers) == Counter(expected_layers)
E       assert Counter({10: 1, 5: 1}) == Counter({11: 1, 4: 1})
E         
E         Left contains 2 more items:
E         {5: 1, 10: 1}
E         Right contains 2 more items:
E         {4: 1, 11: 1}
E         Use -v to get more diff

tests/scheduler_tests/test_layer_allocation.py:84: AssertionError
_________ test_water_filling_rebalance[20-gpu_types2-expected_layers2] _________

num_layers = 20, gpu_types = ['a100-80g', 'rtx5090', 'rtx4090']
expected_layers = [12, 5, 3]

    @pytest.mark.parametrize(
        "num_layers,gpu_types,expected_layers",
        [
            (21, ["a100-80g", "rtx5090", "rtx4090"], [13, 5, 3]),
            (15, ["a100-80g", "rtx5090"], [11, 4]),
            # (20 * 312 : 20 * 165 : 20 * 82.6) / 559.6 = 11.1 : 5.8 : 2.9 -> 12 : 5 : 3
            (20, ["a100-80g", "rtx5090", "rtx4090"], [12, 5, 3]),
            (25, ["a100-80g", "rtx5090", "rtx4090", "rtx4090"], [13, 5, 4, 3]),
            (29, ["rtx4090", "a100-80g", "rtx5090", "rtx5090", "rtx4090"], [3, 13, 5, 5, 3]),
            (8, ["rtx5090", "rtx5090"], [4, 4]),
            (7, ["a100-40g", "rtx5090"], [5, 2]),
        ],
    )
    def test_water_filling_rebalance(num_layers: int, gpu_types: list[str], expected_layers: list[int]):
        """Test water-filling rebalancer with various GPU configurations."""
        model = build_model_info(num_layers)
    
        nodes = [_build_node(g, model, id_suffix=f"-{i}") for i, g in enumerate(gpu_types)]
    
        allocator = GreedyLayerAllocator(model, nodes)
        allocator.adjust_pipeline_layers(nodes, assume_sorted=False)
    
        actual_layers = []
        for node in nodes:
            assert node.start_layer is not None and node.end_layer is not None
            actual_layers.append(node.end_layer - node.start_layer)
    
        assert sum(actual_layers) == num_layers
        # Order-insensitive: only the multiset of stage sizes must match
>       assert Counter(actual_layers) == Counter(expected_layers)
E       assert Counter({11: 1, 6: 1, 3: 1}) == Counter({12: 1, 5: 1, 3: 1})
E         
E         Omitting 1 identical items, use -vv to show
E         Left contains 2 more items:
E         {6: 1, 11: 1}
E         Right contains 2 more items:
E         {5: 1, 12: 1}
E         Use -v to get more diff

tests/scheduler_tests/test_layer_allocation.py:84: AssertionError
_________ test_water_filling_rebalance[25-gpu_types3-expected_layers3] _________

num_layers = 25, gpu_types = ['a100-80g', 'rtx5090', 'rtx4090', 'rtx4090']
expected_layers = [13, 5, 4, 3]

    @pytest.mark.parametrize(
        "num_layers,gpu_types,expected_layers",
        [
            (21, ["a100-80g", "rtx5090", "rtx4090"], [13, 5, 3]),
            (15, ["a100-80g", "rtx5090"], [11, 4]),
            # (20 * 312 : 20 * 165 : 20 * 82.6) / 559.6 = 11.1 : 5.8 : 2.9 -> 12 : 5 : 3
            (20, ["a100-80g", "rtx5090", "rtx4090"], [12, 5, 3]),
            (25, ["a100-80g", "rtx5090", "rtx4090", "rtx4090"], [13, 5, 4, 3]),
            (29, ["rtx4090", "a100-80g", "rtx5090", "rtx5090", "rtx4090"], [3, 13, 5, 5, 3]),
            (8, ["rtx5090", "rtx5090"], [4, 4]),
            (7, ["a100-40g", "rtx5090"], [5, 2]),
        ],
    )
    def test_water_filling_rebalance(num_layers: int, gpu_types: list[str], expected_layers: list[int]):
        """Test water-filling rebalancer with various GPU configurations."""
        model = build_model_info(num_layers)
    
        nodes = [_build_node(g, model, id_suffix=f"-{i}") for i, g in enumerate(gpu_types)]
    
        allocator = GreedyLayerAllocator(model, nodes)
        allocator.adjust_pipeline_layers(nodes, assume_sorted=False)
    
        actual_layers = []
        for node in nodes:
            assert node.start_layer is not None and node.end_layer is not None
            actual_layers.append(node.end_layer - node.start_layer)
    
        assert sum(actual_layers) == num_layers
        # Order-insensitive: only the multiset of stage sizes must match
>       assert Counter(actual_layers) == Counter(expected_layers)
E       assert Counter({3: 2, 13: 1, 6: 1}) == Counter({13: ..., 4: 1, 3: 1})
E         
E         Omitting 1 identical items, use -vv to show
E         Differing items:
E         {3: 2} != {3: 1}
E         Left contains 1 more item:
E         {6: 1}
E         Right contains 2 more items:
E         {4: 1, 5: 1}
E         Use -v to get more diff

tests/scheduler_tests/test_layer_allocation.py:84: AssertionError
_________ test_water_filling_rebalance[29-gpu_types4-expected_layers4] _________

num_layers = 29
gpu_types = ['rtx4090', 'a100-80g', 'rtx5090', 'rtx5090', 'rtx4090']
expected_layers = [3, 13, 5, 5, 3]

    @pytest.mark.parametrize(
        "num_layers,gpu_types,expected_layers",
        [
            (21, ["a100-80g", "rtx5090", "rtx4090"], [13, 5, 3]),
            (15, ["a100-80g", "rtx5090"], [11, 4]),
            # (20 * 312 : 20 * 165 : 20 * 82.6) / 559.6 = 11.1 : 5.8 : 2.9 -> 12 : 5 : 3
            (20, ["a100-80g", "rtx5090", "rtx4090"], [12, 5, 3]),
            (25, ["a100-80g", "rtx5090", "rtx4090", "rtx4090"], [13, 5, 4, 3]),
            (29, ["rtx4090", "a100-80g", "rtx5090", "rtx5090", "rtx4090"], [3, 13, 5, 5, 3]),
            (8, ["rtx5090", "rtx5090"], [4, 4]),
            (7, ["a100-40g", "rtx5090"], [5, 2]),
        ],
    )
    def test_water_filling_rebalance(num_layers: int, gpu_types: list[str], expected_layers: list[int]):
        """Test water-filling rebalancer with various GPU configurations."""
        model = build_model_info(num_layers)
    
        nodes = [_build_node(g, model, id_suffix=f"-{i}") for i, g in enumerate(gpu_types)]
    
        allocator = GreedyLayerAllocator(model, nodes)
        allocator.adjust_pipeline_layers(nodes, assume_sorted=False)
    
        actual_layers = []
        for node in nodes:
            assert node.start_layer is not None and node.end_layer is not None
            actual_layers.append(node.end_layer - node.start_layer)
    
        assert sum(actual_layers) == num_layers
        # Order-insensitive: only the multiset of stage sizes must match
>       assert Counter(actual_layers) == Counter(expected_layers)
E       assert Counter({6: 2, 3: 2, 11: 1}) == Counter({3: 2, 5: 2, 13: 1})
E         
E         Omitting 1 identical items, use -vv to show
E         Left contains 2 more items:
E         {6: 2, 11: 1}
E         Right contains 2 more items:
E         {5: 2, 13: 1}
E         Use -v to get more diff

tests/scheduler_tests/test_layer_allocation.py:84: AssertionError
______________ test_allocator[22-counts2-expected_ranges2-greedy] ______________

num_layers = 22, counts = (2, 2, 0, 2), expected_ranges = [(0, 11), (11, 22)]
strategy = 'greedy'

    @pytest.mark.parametrize(
        "num_layers,counts,expected_ranges,strategy",
        [
            # Six A100-80g: expect two pipelines, 12 each per stage in creation order
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "greedy"),
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "dp"),
            # 22 Layers, capacity (13, 13, 6, 6, 3, 3) -> greedy assigns (11, 11)
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 11),
                    (11, 22),
                ],
                "greedy",
            ),
            # For DP, we expect two pipelines, 13 each per stage in creation order
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 13),
                    (13, 19),
                    (19, 22),
                    (0, 13),
                    (13, 19),
                    (19, 22),
                ],
                "dp",
            ),
            # 14 Layers, capacity (13, 5, 5, 3, 3) -> greedy assigns (10, 4)
            (
                14,
                (1, 0, 2, 2),
                [
                    (0, 10),
                    (10, 14),
                ],
                "greedy",
            ),
            # 7 Layers, capacity (6, 5, 5, 3, 3) -> greedy assigns (5, 2, 4, 3)
            (
                7,
                (0, 1, 2, 2),
                [
                    (0, 5),
                    (5, 7),
                    (0, 4),
                    (4, 7),
                ],
                "greedy",
            ),
        ],
    )
    def test_allocator(
        num_layers: int,
        counts: tuple[int, int, int, int],
        expected_ranges: list[tuple[int, int]],
        strategy: Literal["greedy", "dp"],
    ):
        """Test allocator with greedy and dp strategies."""
        model = build_model_info(num_layers)
    
        n_a100_80g, n_a100_40g, n_5090, n_4090 = counts
    
        nodes: list[Node] = []
        for i in range(n_a100_80g):
            nodes.append(_build_node("a100-80g", model, id_suffix=f"-{i}"))
        for i in range(n_a100_40g):
            nodes.append(_build_node("a100-40g", model, id_suffix=f"-{i}"))
        for i in range(n_5090):
            nodes.append(_build_node("rtx5090", model, id_suffix=f"-{i}"))
        for i in range(n_4090):
            nodes.append(_build_node("rtx4090", model, id_suffix=f"-{i}"))
    
        allocator = (
            GreedyLayerAllocator(model, nodes, assign_left_over_nodes=False)
            if strategy == "greedy"
            else DynamicProgrammingLayerAllocator(model, nodes, assign_left_over_nodes=False)
        )
        allocator.global_allocation()
        _test_gap_patch_rebalance(allocator)
    
        # Collect (start,end) per node in creation order
        actual_ranges: list[tuple[int, int]] = []
        for node in nodes:
            if node.start_layer is None or node.end_layer is None:
                actual_ranges.append((0, 0))
            else:
                actual_ranges.append((node.start_layer, node.end_layer))
    
        # Trim trailing (0,0) if no assignment expected
        actual_trimmed = [r for r in actual_ranges if r != (0, 0)]
        expected_total = sum(e - s for (s, e) in expected_ranges)
>       assert sum(e - s for (s, e) in actual_trimmed) == expected_total
E       assert 44 == 22
E        +  where 44 = sum(<generator object test_allocator.<locals>.<genexpr> at 0x31f271080>)

tests/scheduler_tests/test_layer_allocation.py:222: AssertionError
----------------------------- Captured stdout call -----------------------------
Nov 12 00:01:59.674 [[1m[33mWARNING [0m] layer_allocation.py:667   Node a100-80g-0 has capacity 17
Nov 12 00:01:59.674 [[1m[33mWARNING [0m] layer_allocation.py:667   Node a100-80g-1 has capacity 17
Nov 12 00:01:59.674 [[1m[33mWARNING [0m] layer_allocation.py:667   Node a100-40g-0 has capacity 8
Nov 12 00:01:59.674 [[1m[33mWARNING [0m] layer_allocation.py:667   Node a100-40g-1 has capacity 8
Nov 12 00:01:59.674 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-0 has capacity 5
Nov 12 00:01:59.674 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-1 has capacity 5
------------------------------ Captured log call -------------------------------
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node a100-80g-0 has capacity 17
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node a100-80g-1 has capacity 17
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node a100-40g-0 has capacity 8
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node a100-40g-1 has capacity 8
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-0 has capacity 5
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-1 has capacity 5
________________ test_allocator[22-counts3-expected_ranges3-dp] ________________

num_layers = 22, counts = (2, 2, 0, 2)
expected_ranges = [(0, 13), (13, 19), (19, 22), (0, 13), (13, 19), (19, 22)]
strategy = 'dp'

    @pytest.mark.parametrize(
        "num_layers,counts,expected_ranges,strategy",
        [
            # Six A100-80g: expect two pipelines, 12 each per stage in creation order
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "greedy"),
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "dp"),
            # 22 Layers, capacity (13, 13, 6, 6, 3, 3) -> greedy assigns (11, 11)
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 11),
                    (11, 22),
                ],
                "greedy",
            ),
            # For DP, we expect two pipelines, 13 each per stage in creation order
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 13),
                    (13, 19),
                    (19, 22),
                    (0, 13),
                    (13, 19),
                    (19, 22),
                ],
                "dp",
            ),
            # 14 Layers, capacity (13, 5, 5, 3, 3) -> greedy assigns (10, 4)
            (
                14,
                (1, 0, 2, 2),
                [
                    (0, 10),
                    (10, 14),
                ],
                "greedy",
            ),
            # 7 Layers, capacity (6, 5, 5, 3, 3) -> greedy assigns (5, 2, 4, 3)
            (
                7,
                (0, 1, 2, 2),
                [
                    (0, 5),
                    (5, 7),
                    (0, 4),
                    (4, 7),
                ],
                "greedy",
            ),
        ],
    )
    def test_allocator(
        num_layers: int,
        counts: tuple[int, int, int, int],
        expected_ranges: list[tuple[int, int]],
        strategy: Literal["greedy", "dp"],
    ):
        """Test allocator with greedy and dp strategies."""
        model = build_model_info(num_layers)
    
        n_a100_80g, n_a100_40g, n_5090, n_4090 = counts
    
        nodes: list[Node] = []
        for i in range(n_a100_80g):
            nodes.append(_build_node("a100-80g", model, id_suffix=f"-{i}"))
        for i in range(n_a100_40g):
            nodes.append(_build_node("a100-40g", model, id_suffix=f"-{i}"))
        for i in range(n_5090):
            nodes.append(_build_node("rtx5090", model, id_suffix=f"-{i}"))
        for i in range(n_4090):
            nodes.append(_build_node("rtx4090", model, id_suffix=f"-{i}"))
    
        allocator = (
            GreedyLayerAllocator(model, nodes, assign_left_over_nodes=False)
            if strategy == "greedy"
            else DynamicProgrammingLayerAllocator(model, nodes, assign_left_over_nodes=False)
        )
        allocator.global_allocation()
>       _test_gap_patch_rebalance(allocator)

tests/scheduler_tests/test_layer_allocation.py:209: 
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

allocator = <scheduling.layer_allocation.DynamicProgrammingLayerAllocator object at 0x106507260>

    def _test_gap_patch_rebalance(allocator: BaseLayerAllocator):
        """Sanity checks for gap-patch dynamic rebalancing using allocator state."""
        assert allocator.layer_loads_heap, "Layer loads heap should not be empty"
        assert allocator.node_id_to_node, "Allocator should have node mappings"
        model_info = allocator.model_info
    
        # Heap top should include a host with minimal per-layer KV memory
        per_node_mem = {
            nid: (node.per_decoder_layer_kv_cache_memory or 0)
            for nid, node in allocator.node_id_to_node.items()
        }
        min_mem = min(per_node_mem.values()) if per_node_mem else 0
    
        top_load = allocator.layer_loads_heap[0]
        assert top_load.hosting_nodes
        top_hosts_min = min(per_node_mem.get(h, float("inf")) for h in top_load.hosting_nodes)
>       assert top_hosts_min == min_mem
E       assert 1533916891 == 1288490188

tests/scheduler_tests/test_layer_allocation.py:109: AssertionError
______________ test_allocator[14-counts4-expected_ranges4-greedy] ______________

num_layers = 14, counts = (1, 0, 2, 2), expected_ranges = [(0, 10), (10, 14)]
strategy = 'greedy'

    @pytest.mark.parametrize(
        "num_layers,counts,expected_ranges,strategy",
        [
            # Six A100-80g: expect two pipelines, 12 each per stage in creation order
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "greedy"),
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "dp"),
            # 22 Layers, capacity (13, 13, 6, 6, 3, 3) -> greedy assigns (11, 11)
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 11),
                    (11, 22),
                ],
                "greedy",
            ),
            # For DP, we expect two pipelines, 13 each per stage in creation order
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 13),
                    (13, 19),
                    (19, 22),
                    (0, 13),
                    (13, 19),
                    (19, 22),
                ],
                "dp",
            ),
            # 14 Layers, capacity (13, 5, 5, 3, 3) -> greedy assigns (10, 4)
            (
                14,
                (1, 0, 2, 2),
                [
                    (0, 10),
                    (10, 14),
                ],
                "greedy",
            ),
            # 7 Layers, capacity (6, 5, 5, 3, 3) -> greedy assigns (5, 2, 4, 3)
            (
                7,
                (0, 1, 2, 2),
                [
                    (0, 5),
                    (5, 7),
                    (0, 4),
                    (4, 7),
                ],
                "greedy",
            ),
        ],
    )
    def test_allocator(
        num_layers: int,
        counts: tuple[int, int, int, int],
        expected_ranges: list[tuple[int, int]],
        strategy: Literal["greedy", "dp"],
    ):
        """Test allocator with greedy and dp strategies."""
        model = build_model_info(num_layers)
    
        n_a100_80g, n_a100_40g, n_5090, n_4090 = counts
    
        nodes: list[Node] = []
        for i in range(n_a100_80g):
            nodes.append(_build_node("a100-80g", model, id_suffix=f"-{i}"))
        for i in range(n_a100_40g):
            nodes.append(_build_node("a100-40g", model, id_suffix=f"-{i}"))
        for i in range(n_5090):
            nodes.append(_build_node("rtx5090", model, id_suffix=f"-{i}"))
        for i in range(n_4090):
            nodes.append(_build_node("rtx4090", model, id_suffix=f"-{i}"))
    
        allocator = (
            GreedyLayerAllocator(model, nodes, assign_left_over_nodes=False)
            if strategy == "greedy"
            else DynamicProgrammingLayerAllocator(model, nodes, assign_left_over_nodes=False)
        )
        allocator.global_allocation()
        _test_gap_patch_rebalance(allocator)
    
        # Collect (start,end) per node in creation order
        actual_ranges: list[tuple[int, int]] = []
        for node in nodes:
            if node.start_layer is None or node.end_layer is None:
                actual_ranges.append((0, 0))
            else:
                actual_ranges.append((node.start_layer, node.end_layer))
    
        # Trim trailing (0,0) if no assignment expected
        actual_trimmed = [r for r in actual_ranges if r != (0, 0)]
        expected_total = sum(e - s for (s, e) in expected_ranges)
>       assert sum(e - s for (s, e) in actual_trimmed) == expected_total
E       assert 28 == 14
E        +  where 28 = sum(<generator object test_allocator.<locals>.<genexpr> at 0x3219de400>)

tests/scheduler_tests/test_layer_allocation.py:222: AssertionError
----------------------------- Captured stdout call -----------------------------
Nov 12 00:01:59.680 [[1m[33mWARNING [0m] layer_allocation.py:667   Node a100-80g-0 has capacity 17
Nov 12 00:01:59.680 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx5090-0 has capacity 6
Nov 12 00:01:59.680 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx5090-1 has capacity 6
Nov 12 00:01:59.680 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-0 has capacity 5
Nov 12 00:01:59.680 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-1 has capacity 5
------------------------------ Captured log call -------------------------------
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node a100-80g-0 has capacity 17
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx5090-0 has capacity 6
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx5090-1 has capacity 6
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-0 has capacity 5
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-1 has capacity 5
______________ test_allocator[7-counts5-expected_ranges5-greedy] _______________

num_layers = 7, counts = (0, 1, 2, 2)
expected_ranges = [(0, 5), (5, 7), (0, 4), (4, 7)], strategy = 'greedy'

    @pytest.mark.parametrize(
        "num_layers,counts,expected_ranges,strategy",
        [
            # Six A100-80g: expect two pipelines, 12 each per stage in creation order
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "greedy"),
            (36, (6, 0, 0, 0), [(0, 12), (12, 24), (24, 36), (0, 12), (12, 24), (24, 36)], "dp"),
            # 22 Layers, capacity (13, 13, 6, 6, 3, 3) -> greedy assigns (11, 11)
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 11),
                    (11, 22),
                ],
                "greedy",
            ),
            # For DP, we expect two pipelines, 13 each per stage in creation order
            (
                22,
                (2, 2, 0, 2),
                [
                    (0, 13),
                    (13, 19),
                    (19, 22),
                    (0, 13),
                    (13, 19),
                    (19, 22),
                ],
                "dp",
            ),
            # 14 Layers, capacity (13, 5, 5, 3, 3) -> greedy assigns (10, 4)
            (
                14,
                (1, 0, 2, 2),
                [
                    (0, 10),
                    (10, 14),
                ],
                "greedy",
            ),
            # 7 Layers, capacity (6, 5, 5, 3, 3) -> greedy assigns (5, 2, 4, 3)
            (
                7,
                (0, 1, 2, 2),
                [
                    (0, 5),
                    (5, 7),
                    (0, 4),
                    (4, 7),
                ],
                "greedy",
            ),
        ],
    )
    def test_allocator(
        num_layers: int,
        counts: tuple[int, int, int, int],
        expected_ranges: list[tuple[int, int]],
        strategy: Literal["greedy", "dp"],
    ):
        """Test allocator with greedy and dp strategies."""
        model = build_model_info(num_layers)
    
        n_a100_80g, n_a100_40g, n_5090, n_4090 = counts
    
        nodes: list[Node] = []
        for i in range(n_a100_80g):
            nodes.append(_build_node("a100-80g", model, id_suffix=f"-{i}"))
        for i in range(n_a100_40g):
            nodes.append(_build_node("a100-40g", model, id_suffix=f"-{i}"))
        for i in range(n_5090):
            nodes.append(_build_node("rtx5090", model, id_suffix=f"-{i}"))
        for i in range(n_4090):
            nodes.append(_build_node("rtx4090", model, id_suffix=f"-{i}"))
    
        allocator = (
            GreedyLayerAllocator(model, nodes, assign_left_over_nodes=False)
            if strategy == "greedy"
            else DynamicProgrammingLayerAllocator(model, nodes, assign_left_over_nodes=False)
        )
        allocator.global_allocation()
        _test_gap_patch_rebalance(allocator)
    
        # Collect (start,end) per node in creation order
        actual_ranges: list[tuple[int, int]] = []
        for node in nodes:
            if node.start_layer is None or node.end_layer is None:
                actual_ranges.append((0, 0))
            else:
                actual_ranges.append((node.start_layer, node.end_layer))
    
        # Trim trailing (0,0) if no assignment expected
        actual_trimmed = [r for r in actual_ranges if r != (0, 0)]
        expected_total = sum(e - s for (s, e) in expected_ranges)
>       assert sum(e - s for (s, e) in actual_trimmed) == expected_total
E       assert 21 == 14
E        +  where 21 = sum(<generator object test_allocator.<locals>.<genexpr> at 0x3219df920>)

tests/scheduler_tests/test_layer_allocation.py:222: AssertionError
----------------------------- Captured stdout call -----------------------------
Nov 12 00:01:59.683 [[1m[33mWARNING [0m] layer_allocation.py:667   Node a100-40g-0 has capacity 8
Nov 12 00:01:59.683 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx5090-0 has capacity 6
Nov 12 00:01:59.683 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx5090-1 has capacity 6
Nov 12 00:01:59.683 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-0 has capacity 5
Nov 12 00:01:59.683 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-1 has capacity 5
------------------------------ Captured log call -------------------------------
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node a100-40g-0 has capacity 8
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx5090-0 has capacity 6
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx5090-1 has capacity 6
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-0 has capacity 5
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-1 has capacity 5
______________ test_pipeline_required_with_midrange_only[greedy] _______________

strategy = 'greedy'

    @pytest.mark.parametrize("strategy", ["greedy", "dp"])
    def test_pipeline_required_with_midrange_only(strategy: Literal["greedy", "dp"]):
        """Model requires pipeline across multiple mid-range GPUs (RTX4090)."""
        model = build_model_info(7)
        nodes = [_build_node("rtx4090", model, id_suffix=f"-{i}") for i in range(3)]
        alloc = (
            GreedyLayerAllocator(model, nodes)
            if strategy == "greedy"
            else DynamicProgrammingLayerAllocator(model, nodes)
        )
        ok = alloc.global_allocation()
        assert ok is True
        # At least two nodes should be assigned to cover 7 layers
        assigned = [(n.node_id, n.start_layer, n.end_layer) for n in nodes if n.start_layer is not None]
        assert len(assigned) >= 2
        total = sum(e - s for _, s, e in assigned)
>       assert total == model.num_layers
E       AssertionError: assert 15 == 7
E        +  where 7 = ModelInfo(model_name='GPUOss-7L', mlx_model_name='MLXOss-7L', head_size=64, hidden_dim=2880, intermediate_dim=2880, nu...element=2, embedding_bytes_per_element=2, qk_nope_head_dim=None, qk_rope_head_dim=None, head_size_k=64, head_size_v=64).num_layers

tests/scheduler_tests/test_layer_allocation.py:281: AssertionError
----------------------------- Captured stdout call -----------------------------
Nov 12 00:01:59.686 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-0 has capacity 5
Nov 12 00:01:59.686 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-1 has capacity 5
Nov 12 00:01:59.686 [[1m[33mWARNING [0m] layer_allocation.py:667   Node rtx4090-2 has capacity 5
------------------------------ Captured log call -------------------------------
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-0 has capacity 5
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-1 has capacity 5
WARNING  scheduling.layer_allocation:layer_allocation.py:667 Node rtx4090-2 has capacity 5
________________ test_pipeline_required_with_midrange_only[dp] _________________

strategy = 'dp'

    @pytest.mark.parametrize("strategy", ["greedy", "dp"])
    def test_pipeline_required_with_midrange_only(strategy: Literal["greedy", "dp"]):
        """Model requires pipeline across multiple mid-range GPUs (RTX4090)."""
        model = build_model_info(7)
        nodes = [_build_node("rtx4090", model, id_suffix=f"-{i}") for i in range(3)]
        alloc = (
            GreedyLayerAllocator(model, nodes)
            if strategy == "greedy"
            else DynamicProgrammingLayerAllocator(model, nodes)
        )
        ok = alloc.global_allocation()
        assert ok is True
        # At least two nodes should be assigned to cover 7 layers
        assigned = [(n.node_id, n.start_layer, n.end_layer) for n in nodes if n.start_layer is not None]
        assert len(assigned) >= 2
        total = sum(e - s for _, s, e in assigned)
>       assert total == model.num_layers
E       AssertionError: assert 15 == 7
E        +  where 7 = ModelInfo(model_name='GPUOss-7L', mlx_model_name='MLXOss-7L', head_size=64, hidden_dim=2880, intermediate_dim=2880, nu...element=2, embedding_bytes_per_element=2, qk_nope_head_dim=None, qk_rope_head_dim=None, head_size_k=64, head_size_v=64).num_layers

tests/scheduler_tests/test_layer_allocation.py:281: AssertionError
=========================== short test summary info ============================
FAILED tests/scheduler_tests/test_layer_allocation.py::test_water_filling_rebalance[21-gpu_types0-expected_layers0]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_water_filling_rebalance[15-gpu_types1-expected_layers1]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_water_filling_rebalance[20-gpu_types2-expected_layers2]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_water_filling_rebalance[25-gpu_types3-expected_layers3]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_water_filling_rebalance[29-gpu_types4-expected_layers4]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_allocator[22-counts2-expected_ranges2-greedy]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_allocator[22-counts3-expected_ranges3-dp]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_allocator[14-counts4-expected_ranges4-greedy]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_allocator[7-counts5-expected_ranges5-greedy]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_pipeline_required_with_midrange_only[greedy]
FAILED tests/scheduler_tests/test_layer_allocation.py::test_pipeline_required_with_midrange_only[dp]
======================== 11 failed, 56 passed in 5.62s =========================

```

