package com.anycc.snova.sports.venue.service.impl;

import com.anycc.snova.core.sys.entity.Org;
import com.anycc.snova.core.utils.PagingUtil;
import com.anycc.snova.dto.query.PagingRequest;
import com.anycc.snova.dto.query.PagingResponse;
import com.anycc.snova.dto.select.Option;
import com.anycc.snova.sports.venue.entity.SiteMaintain;
import com.anycc.snova.sports.venue.repository.SiteMaintainRepository;
import com.anycc.snova.sports.venue.service.SiteMaintainService;
import org.apache.commons.lang3.StringUtils;
import org.apache.shiro.util.Assert;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.domain.Specification;
import org.springframework.stereotype.Service;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Predicate;
import javax.persistence.criteria.Root;
import java.util.ArrayList;
import java.util.List;


@Service("siteMaintainService")
public class SiteMaintainServiceImpl implements SiteMaintainService {

    @Autowired
    SiteMaintainRepository siteMaintainRepository;

    @Override
    public SiteMaintain findByVenueName(Org venue) {
        return new SiteMaintain();
    }

    @Override
    public PagingResponse<SiteMaintain> findByPaging(SiteMaintain siteMaintain, PagingRequest pagingRequest) {
        Pageable pageable = PagingUtil.buildPageRequest(pagingRequest);
        Page<SiteMaintain> siteMaintainPage = siteMaintainRepository.findAll(createSpecification(siteMaintain), pageable);
        return PagingUtil.buildPagingResponse(pagingRequest, siteMaintainPage);
    }

    @Override
    public void delete(String id){
        siteMaintainRepository.delete(id);
    }

    /**
     * 根据id查找场馆维护信息
     * @param id
     * @return
     */
    @Override
    public SiteMaintain findById(String id){
        SiteMaintain siteMaintain= siteMaintainRepository.findOne(id);
        System.out.println(siteMaintain.toString());
        Assert.notNull(id, "id不为空");
        return siteMaintain;
    }

    /**
     * 获取场馆名称
     * @return list
     */
    @Override
    public List<Option> getVenueNameList(){
        Org org=new Org();
        List<Org> orgList=new ArrayList<>();
        /*
        Org org=new Org();
        List<Org> orgList=org.getChildren();
        List<Option> options=new ArrayList<>();
        for (Org org1:orgList){
            for (Option option:options){
                option.setValue(org1.getId());
                option.setText(org1.getName());
            }
        }
        return options;
        */
        List<Option> venueNameList=new ArrayList<>();
        Option option;

        for (int i=0;i<4;i++){
            option=new Option();
            if (i==0){
                option.setValue("0");
                option.setText("体育馆");
                option.setSelected(true);
            }
            if (i==1){
                option.setValue("1");
                option.setText("体育场");
                option.setSelected(true);
            }
            if (i==2){
                option.setValue("2");
                option.setText("游泳馆");
                option.setSelected(true);
            }
            if (i==3){
                option.setValue("3");
                option.setText("综合馆");
                option.setSelected(true);
            }
            venueNameList.add(option);
        }
        return venueNameList;
    }

    /**
     * 创建维护信息
     * @param siteMaintain
     */
   @Override
   public void create(SiteMaintain siteMaintain){
      siteMaintainRepository.save(siteMaintain);
   }

    /**
     * 更新维护信息
     * @param siteMaintain
     */
    @Override
    public void update(SiteMaintain siteMaintain){
        Assert.notNull(siteMaintain.getId(),"场馆维护id不能为空");
        siteMaintainRepository.save(siteMaintain);
    }

    /**
     * 创建动态查询条件
     *
     * @param siteMaintain 查询参数
     * @return Specification
     */
    private Specification<SiteMaintain> createSpecification(SiteMaintain siteMaintain) {
        return new Specification<SiteMaintain>() {
            @Override
            public Predicate toPredicate(Root<SiteMaintain> root, CriteriaQuery<?> query,
                                         CriteriaBuilder criteriaBuilder) {
                List<Predicate> predicateList = createPredicateList(root, criteriaBuilder, siteMaintain);
                Predicate[] predicateArr = new Predicate[predicateList.size()];
                return criteriaBuilder.and(predicateList.toArray(predicateArr));
            }
        };
    }

    /**
     * 创建Predicate列表
     *
     * @param root            Root
     * @param criteriaBuilder CriteriaBuilder
     * @param siteMaintain    查询条件
     * @return Predicate列表
     */
    private List<Predicate> createPredicateList(Root<SiteMaintain> root, CriteriaBuilder criteriaBuilder,
                                                SiteMaintain siteMaintain) {
        List<Predicate> predicateList = new ArrayList<>();
        if (StringUtils.isNotBlank(siteMaintain.getResponsible())){
            predicateList.add(criteriaBuilder.equal(root.get("responsible"), siteMaintain.getResponsible()));
        }
        if (StringUtils.isNotBlank(siteMaintain.getPhone())) {
            predicateList.add(criteriaBuilder.equal(root.get("phone"), siteMaintain.getPhone()));
        }
        if (StringUtils.isNotBlank(siteMaintain.getProject())) {
            predicateList.add(criteriaBuilder.equal(root.get("project"), siteMaintain.getProject()));
        }
        return predicateList;
    }

}



